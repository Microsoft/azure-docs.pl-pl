---
title: Uwierzytelnianie urządzeń podrzędnych — Azure IoT Edge | Microsoft Docs
description: Jak uwierzytelniać urządzenia podrzędne lub urządzenia typu liść w celu IoT Hub i kierować ich połączenia za poorednictwem urządzeń bram Azure IoT Edge Gateway.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/15/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d9e3e0f96d235829928c1f7c79864b1dc732f9e4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046350"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Uwierzytelnianie urządzenia podrzędnego w usłudze Azure IoT Hub

W przypadku niejawnego scenariusza bramy urządzenia podrzędne (nazywane czasami urządzeniami typu liść lub urządzeniami podrzędnymi) potrzebują tożsamości w IoT Hub jak każde inne urządzenie. W tym artykule opisano opcje uwierzytelniania urządzenia podrzędnego w IoT Hub, a następnie pokazano, jak zadeklarować połączenie bramy.

Należy wykonać trzy ogólne kroki, aby skonfigurować pomyślne, przezroczyste połączenie bramy. W tym artykule omówiono drugi krok:

1. Skonfiguruj urządzenie bramy jako serwer, aby urządzenia podrzędne mogły bezpiecznie się z nim połączyć. Skonfiguruj bramę do odbierania komunikatów z urządzeń podrzędnych i Roześlij je do odpowiednich miejsc docelowych. Aby zapoznać się z tymi krokami, zobacz [Konfigurowanie urządzenia IoT Edge jako nieprzezroczystej bramy](how-to-create-transparent-gateway.md).
2. **Utwórz tożsamość urządzenia dla urządzenia podrzędnego, aby można było uwierzytelnić się za pomocą IoT Hub. Skonfiguruj urządzenie podrzędne do wysyłania komunikatów za pomocą urządzenia bramy.**
3. Podłącz urządzenie podrzędne do urządzenia bramy i Rozpocznij wysyłanie komunikatów. Aby zapoznać się z tymi krokami, zobacz [łączenie urządzenia podrzędnego z bramą Azure IoT Edge](how-to-connect-downstream-device.md).

Urządzenia podrzędne mogą uwierzytelniać się za pomocą IoT Hub przy użyciu jednej z trzech metod: klucze symetryczne (czasami określane jako klucze dostępu współdzielonego), certyfikaty z podpisem własnym X. 509 lub certyfikaty z certyfikatem X. 509 (CA). Kroki uwierzytelniania są podobne do kroków użytych w celu skonfigurowania dowolnego urządzenia niezwiązanego z usługą IoT-Edge z IoT Hub z małymi różnicami w celu zadeklarować relacji bramy.

Automatyczne Inicjowanie obsługi administracyjnej urządzeń podrzędnych przy użyciu usługi Azure IoT Hub Device Provisioning Service (DPS) nie jest obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki opisane w [sekcji Konfigurowanie urządzenia IoT Edge do działania jako nieprzezroczyste bramy](how-to-create-transparent-gateway.md).

Jeśli używasz uwierzytelniania X. 509, będziesz generować certyfikaty dla urządzenia podrzędnego. Mieć ten sam certyfikat głównego urzędu certyfikacji i skrypt generujący certyfikat, który został użyty dla przezroczystego artykułu bramy, który jest dostępny do ponownego użycia.

Ten artykuł odnosi się do *nazwy hosta bramy* w kilku punktach. Nazwa hosta bramy jest zadeklarowana w parametrze **hostname** pliku konfiguracyjnego na urządzeniu bramy IoT Edge. Jest on określany w parametrach połączenia urządzenia podrzędnego. Nazwa hosta bramy musi być rozpoznawalna na adres IP przy użyciu systemu DNS lub wpisu pliku hosta na urządzeniu podrzędnym.

## <a name="register-device-with-iot-hub"></a>Rejestrowanie urządzenia w IoT Hub

Wybierz, w jaki sposób urządzenie podrzędne ma być uwierzytelniane przy użyciu IoT Hub:

* [Uwierzytelnianie przy użyciu klucza symetrycznego](#symmetric-key-authentication): IoT Hub tworzy klucz umieszczony na urządzeniu podrzędnym. Gdy urządzenie zostanie uwierzytelnione, IoT Hub sprawdza, czy dwa klucze pasują do siebie. Nie trzeba tworzyć dodatkowych certyfikatów do korzystania z uwierzytelniania przy użyciu klucza symetrycznego.

  Ta metoda jest szybsza, aby rozpocząć pracę w przypadku testowania bram w scenariuszu projektowania lub testowania.

* Uwierzytelnianie z podpisem [własnym x. 509](#x509-self-signed-authentication): czasami nazywane uwierzytelnianiem odcisku palca, ponieważ odcisk palca jest udostępniany z certyfikatu X. 509 urządzenia z IoT Hub.

  Uwierzytelnianie certyfikatu jest zalecane w przypadku urządzeń w scenariuszach produkcyjnych.

* [Uwierzytelnianie przy użyciu urzędu certyfikacji X. 509](#x509-ca-signed-authentication): Przekaż certyfikat głównego urzędu certyfikacji do IoT Hub. Gdy urządzenia przedstawiają swój certyfikat X. 509 na potrzeby uwierzytelniania, IoT Hub sprawdza, czy należy do łańcucha zaufania podpisanego przez ten sam certyfikat głównego urzędu certyfikacji.

  Uwierzytelnianie certyfikatu jest zalecane w przypadku urządzeń w scenariuszach produkcyjnych.

### <a name="symmetric-key-authentication"></a>Uwierzytelnianie za pomocą klucza zawartości

Uwierzytelnianie przy użyciu klucza symetrycznego lub uwierzytelnianie klucza dostępu współdzielonego jest najprostszym sposobem na uwierzytelnianie za pomocą IoT Hub. W przypadku uwierzytelniania przy użyciu klucza symetrycznego klucz Base64 jest skojarzony z IDENTYFIKATORem urządzenia IoT w IoT Hub. Ten klucz jest dołączany do aplikacji IoT, aby urządzenie mógł je przedstawić podczas łączenia się z IoT Hub.

Dodaj nowe urządzenie IoT w centrum IoT Hub przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure lub rozszerzenia IoT dla Visual Studio Code. Należy pamiętać, że urządzenia podrzędne muszą być identyfikowane w IoT Hub jako regularne urządzenia IoT, a nie IoT Edge urządzeń.

Podczas tworzenia nowej tożsamości urządzenia podaj następujące informacje:

* Utwórz identyfikator urządzenia.

* Wybierz **klucz symetryczny** jako typ uwierzytelniania.

* Wybierz pozycję **Ustaw urządzenie nadrzędne** i wybierz urządzenie bramy IoT Edge, za pomocą którego będzie nawiązywane połączenie. Możesz zawsze zmienić element nadrzędny później.

   ![Tworzenie identyfikatora urządzenia z uwierzytelnianiem przy użyciu klucza symetrycznego w portalu](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

   >[!NOTE]
   >Ustawienie urządzenia nadrzędnego jako opcjonalnego dla urządzeń podrzędnych korzystających z uwierzytelniania przy użyciu klucza symetrycznego. Jednak począwszy od IoT Edge wersja 1.1.0 każde urządzenie podrzędne musi być przypisane do urządzenia nadrzędnego.
   >
   >Można skonfigurować Centrum IoT Edge, aby powrócić do poprzedniego zachowania przez ustawienie zmiennej środowiskowej **authenticationMode** na wartość **CloudAndScope**.

Możesz również użyć [rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension) , aby ukończyć tę samą operację. W poniższym przykładzie za pomocą polecenia [AZ IoT Hub Device-Identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) można utworzyć nowe urządzenie IoT z uwierzytelnianiem przy użyciu klucza symetrycznego i przypisać urządzenie nadrzędne:

```azurecli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

Następnie należy [pobrać i zmodyfikować parametry połączenia](#retrieve-and-modify-connection-string) , aby urządzenie znało połączenie za pośrednictwem bramy.

### <a name="x509-self-signed-authentication"></a>Uwierzytelnianie z podpisem własnym X. 509

W przypadku uwierzytelniania z podpisem własnym X. 509, czasami określanego jako Uwierzytelnianie odciskiem palca, należy utworzyć certyfikaty do umieszczenia na urządzeniu podrzędnym. Te certyfikaty mają odcisk palca, które udostępniają IoT Hub na potrzeby uwierzytelniania.

1. Przy użyciu certyfikatu urzędu certyfikacji Utwórz dwa certyfikaty urządzeń (podstawowe i pomocnicze) dla urządzenia podrzędnego.

   Jeśli nie masz urzędu certyfikacji do tworzenia certyfikatów X. 509, możesz użyć skryptów certyfikatu demonstracyjnego IoT Edge do [tworzenia certyfikatów urządzeń podrzędnych](how-to-create-test-certificates.md#create-downstream-device-certificates). Postępuj zgodnie z instrukcjami dotyczącymi tworzenia certyfikatów z podpisem własnym. Użyj tego samego certyfikatu głównego urzędu certyfikacji, który wygenerował certyfikaty dla urządzenia bramy.

   W przypadku tworzenia własnych certyfikatów upewnij się, że nazwa podmiotu certyfikatu urządzenia jest ustawiona na identyfikator urządzenia używany podczas rejestrowania urządzenia IoT w usłudze Azure IoT Hub. To ustawienie jest wymagane do uwierzytelniania.

2. Pobierz odcisk palca SHA1 (nazywany odciskiem palca w interfejsie IoT Hub) z każdego certyfikatu, który jest 40m ciągiem znaków w formacie szesnastkowym. Użyj następującego polecenia OpenSSL, aby wyświetlić certyfikat i znaleźć odcisk palca:

   * W systemie Windows:

     ```PowerShell
     openssl x509 -in <path to primary device certificate>.cert.pem -text -fingerprint
     ```

   * W systemie Linux:

     ```Bash
     openssl x509 -in <path to primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
     ```

   Uruchom to polecenie dwukrotnie, raz dla certyfikatu podstawowego i jeden raz dla certyfikatu pomocniczego. Podczas rejestrowania nowego urządzenia IoT za pomocą certyfikatów X. 509 z podpisem własnym można podawać odciski palców dla obu certyfikatów.

3. Przejdź do centrum IoT Hub w Azure Portal i Utwórz nową tożsamość urządzenia IoT o następujących wartościach:

   * Podaj **Identyfikator urządzenia** , który jest zgodny z nazwą podmiotu certyfikatów urządzeń.
   * Wybierz pozycję **X. 509 z podpisem własnym** jako typ uwierzytelniania.
   * Wklej ciągi szesnastkowe skopiowane z podstawowych i pomocniczych certyfikatów urządzenia.
   * Wybierz pozycję **Ustaw urządzenie nadrzędne** i wybierz urządzenie bramy IoT Edge, za pomocą którego będzie nawiązywane połączenie. Możesz zawsze zmienić element nadrzędny później.

   ![Tworzenie identyfikatora urządzenia z uwierzytelnianiem z podpisem własnym X. 509 w portalu](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. Skopiuj zarówno certyfikat podstawowy, jak i pomocniczy, a także ich klucze do dowolnej lokalizacji na urządzeniu podrzędnym. Należy również przenieść kopię certyfikatu udostępnionego głównego urzędu certyfikacji, który wygenerował zarówno certyfikat urządzenia bramy, jak i certyfikaty urządzeń podrzędnych.

   Te pliki certyfikatów można odwoływać do wszystkich aplikacji na urządzeniu podrzędnym, które nawiązują połączenie z IoT Hub. Do przenoszenia plików certyfikatów można użyć usługi, takiej jak [Azure Key Vault](../key-vault/index.yml) lub funkcja, taka jak [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) .

5. W zależności od preferowanego języka zapoznaj się z przykładami, w jaki sposób można odwoływać się do certyfikatów X. 509 w aplikacjach IoT:

   * C#: [Konfigurowanie zabezpieczeń X. 509 w usłudze Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample. c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509. Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509. PR](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

Możesz również użyć [rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension) , aby ukończyć tę samą operację tworzenia urządzenia. W poniższym przykładzie za pomocą polecenia [AZ IoT Hub Device-Identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) można utworzyć nowe urządzenie IoT z uwierzytelnianiem z podpisem własnym X. 509 i przypisywać urządzenie nadrzędne:

```azurecli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Następnie należy [pobrać i zmodyfikować parametry połączenia](#retrieve-and-modify-connection-string) , aby urządzenie znało połączenie za pośrednictwem bramy.

### <a name="x509-ca-signed-authentication"></a>Uwierzytelnianie za podpisanym urzędem certyfikacji X. 509

W przypadku uwierzytelniania podpisanego za pomocą certyfikatu X. 509 urząd certyfikacji wymagany jest certyfikat głównego urzędu certyfikacji zarejestrowany w IoT Hub używany do podpisywania certyfikatów dla urządzenia podrzędnego. Uwierzytelnianie będzie możliwe przy użyciu dowolnego urządzenia korzystającego z certyfikatu z certyfikatem głównego urzędu certyfikacji lub dowolnego z jego certyfikatów pośrednich.

Ta sekcja jest oparta na instrukcji przedstawionych w IoT Hub artykule [Konfigurowanie zabezpieczeń X. 509 w usłudze Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md).

1. Przy użyciu certyfikatu urzędu certyfikacji Utwórz dwa certyfikaty urządzeń (podstawowe i pomocnicze) dla urządzenia podrzędnego.

   Jeśli nie masz urzędu certyfikacji do tworzenia certyfikatów X. 509, możesz użyć skryptów certyfikatu demonstracyjnego IoT Edge do [tworzenia certyfikatów urządzeń podrzędnych](how-to-create-test-certificates.md#create-downstream-device-certificates). Postępuj zgodnie z instrukcjami dotyczącymi tworzenia certyfikatów podpisanych przez urząd certyfikacji. Użyj tego samego certyfikatu głównego urzędu certyfikacji, który wygenerował certyfikaty dla urządzenia bramy.

2. Postępuj zgodnie z instrukcjami znajdującymi się w sekcji [Rejestrowanie certyfikatów urzędu certyfikacji x. 509 w usłudze IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) tematu *Konfigurowanie zabezpieczeń X. 509 w usłudze Azure IoT Hub*. W tej sekcji wykonaj następujące czynności:

   1. Przekaż certyfikat głównego urzędu certyfikacji. Jeśli używasz certyfikatów demonstracyjnych, główny urząd certyfikacji jest **\<path> /certs/Azure-IoT-test-Only.root.ca.CERT.pem**.

   2. Sprawdź, czy jesteś własnym certyfikatem głównego urzędu certyfikacji.

3. Postępuj zgodnie z instrukcjami podanymi w sekcji [Tworzenie urządzenia x. 509 dla Centrum IoT](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) *w temacie Konfigurowanie zabezpieczeń X. 509 w usłudze Azure IoT Hub*. W tej sekcji wykonaj następujące czynności:

   1. Dodaj nowe urządzenie. Podaj nazwę z małymi literami dla **identyfikatora urządzenia**, a następnie wybierz pozycję typ uwierzytelniania **X. 509 podpisany przez urząd certyfikacji**.

   2. Ustaw urządzenie nadrzędne. Wybierz pozycję **Ustaw urządzenie nadrzędne** i wybierz urządzenie bramy IoT Edge, które będzie zapewniać połączenie IoT Hub.

4. Utwórz łańcuch certyfikatów dla urządzenia podrzędnego. Użyj tego samego certyfikatu głównego urzędu certyfikacji, który został przekazany do IoT Hub, aby utworzyć ten łańcuch. Użyj tego samego identyfikatora urządzenia małymi literami, który został przekazany do tożsamości urządzenia w portalu.

5. Skopiuj certyfikat i klucze urządzenia do dowolnej lokalizacji na urządzeniu podrzędnym. Należy również przenieść kopię certyfikatu udostępnionego głównego urzędu certyfikacji, który wygenerował zarówno certyfikat urządzenia bramy, jak i certyfikaty urządzeń podrzędnych.

   Te pliki zostaną odwołane do wszystkich aplikacji na urządzeniu podrzędnym, które nawiązują połączenie z IoT Hub. Do przenoszenia plików certyfikatów można użyć usługi, takiej jak [Azure Key Vault](../key-vault/index.yml) lub funkcja, taka jak [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) .

6. W zależności od preferowanego języka zapoznaj się z przykładami, w jaki sposób można odwoływać się do certyfikatów X. 509 w aplikacjach IoT:

   * C#: [Konfigurowanie zabezpieczeń X. 509 w usłudze Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample. c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509. Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509. PR](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

Możesz również użyć [rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension) , aby ukończyć tę samą operację tworzenia urządzenia. W poniższym przykładzie za pomocą polecenia [AZ IoT Hub Device-Identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) można utworzyć nowe urządzenie IoT przy użyciu podpisanego uwierzytelniania urzędu certyfikacji X. 509 i przypisywać urządzenie nadrzędne:

```azurecli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Następnie należy [pobrać i zmodyfikować parametry połączenia](#retrieve-and-modify-connection-string) , aby urządzenie znało połączenie za pośrednictwem bramy.

## <a name="retrieve-and-modify-connection-string"></a>Pobieranie i modyfikowanie parametrów połączenia

Po utworzeniu tożsamości urządzenia IoT w portalu można pobrać jej klucze podstawowe lub pomocnicze. Jeden z tych kluczy musi być uwzględniony w parametrach połączenia używanych przez aplikacje do komunikowania się z IoT Hub. W przypadku uwierzytelniania przy użyciu klucza symetrycznego IoT Hub zapewnia w pełni uformowane parametry połączenia w szczegółach urządzenia dla wygody użytkownika. Należy dodać dodatkowe informacje o urządzeniu bramy do parametrów połączenia.

Parametry połączenia dla urządzeń podrzędnych muszą mieć następujące składniki:

* Centrum IoT, z którym łączy się urządzenie: `Hostname={iothub name}.azure-devices.net`
* Identyfikator urządzenia zarejestrowany w centrum: `DeviceID={device ID}`
* Metoda uwierzytelniania, bez względu na to, czy klucz symetryczny lub certyfikat X. 509
  * W przypadku korzystania z uwierzytelniania przy użyciu klucza symetrycznego Podaj klucz podstawowy lub pomocniczy: `SharedAccessKey={key}`
  * Jeśli używasz uwierzytelniania certyfikatu X. 509, podaj flagę: `x509=true`
* Urządzenie bramy, za pomocą którego urządzenie nawiązuje połączenie. Podaj wartość **hostname** z pliku konfiguracyjnego urządzenia bramy IoT Edge: `GatewayHostName={gateway hostname}`

Wszystkie razem, kompletne parametry połączenia wyglądają następująco:

```console
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Oraz

```console
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;x509=true;GatewayHostName=myGatewayDevice
```

Dzięki relacji nadrzędny/podrzędny można uprościć parametry połączenia przez wywołanie bramy bezpośrednio jako hosta połączenia. Na przykład:

```console
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

Te zmodyfikowane parametry połączenia będą używane w następnym artykule niewidocznej serii bramy.

## <a name="next-steps"></a>Następne kroki

W tym momencie masz urządzenie IoT Edge zarejestrowane w centrum IoT Hub i skonfigurowano je jako niejawną bramę. Masz również zarejestrowane urządzenie podrzędne w centrum IoT Hub i wskazanie jego urządzenia bramy.

Następnie należy skonfigurować urządzenie podrzędne, aby ufać urządzeniu bramy i bezpiecznie połączyć się z nim. Przejdź do następnego artykułu z niewidocznej serii bram, [Podłącz urządzenie podrzędne do bramy Azure IoT Edge](how-to-connect-downstream-device.md).