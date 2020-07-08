---
title: Tworzenie przezroczystego urządzenia bramy — Azure IoT Edge | Microsoft Docs
description: Używanie urządzenia Azure IoT Edge jako przezroczystej bramy, która może przetwarzać informacje z urządzeń podrzędnych
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 0155294777e1d732e5ff3874102b90049d9a123d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84782589"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Konfigurowanie urządzenia usługi IoT Edge, aby działało jako przezroczysta brama

Ten artykuł zawiera szczegółowe instrukcje dotyczące konfigurowania urządzenia IoT Edge jako przezroczystej bramy dla innych urządzeń w celu komunikowania się z IoT Hub. W tym artykule jest wykorzystywana *IoT Edge Brama* do odwoływania się do urządzenia IoT Edge skonfigurowanego jako nieprzezroczysta brama. Aby uzyskać więcej informacji, zobacz [jak urządzenie IoT Edge może być używane jako brama](./iot-edge-as-gateway.md).

>[!NOTE]
>Aktualne
>
> * Urządzenia z włączoną krawędzią nie mogą łączyć się z bramami IoT Edge.
> * Urządzenia podrzędne nie mogą używać przekazywania plików.

Należy wykonać trzy ogólne kroki, aby skonfigurować pomyślne, przezroczyste połączenie bramy. W tym artykule omówiono pierwszy krok:

1. **Skonfiguruj urządzenie bramy jako serwer, aby urządzenia podrzędne mogły bezpiecznie się z nim połączyć. Skonfiguruj bramę do odbierania komunikatów z urządzeń podrzędnych i Roześlij je do odpowiednich miejsc docelowych.**
2. Utwórz tożsamość urządzenia dla urządzenia podrzędnego, aby można było uwierzytelnić się za pomocą IoT Hub. Skonfiguruj urządzenie podrzędne do wysyłania komunikatów za pomocą urządzenia bramy. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie urządzenia podrzędnego w usłudze Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. Podłącz urządzenie podrzędne do urządzenia bramy i Rozpocznij wysyłanie komunikatów. Aby uzyskać więcej informacji, zobacz [łączenie urządzenia podrzędnego z bramą Azure IoT Edge](how-to-connect-downstream-device.md).

Aby urządzenie działało jako brama, musi bezpiecznie połączyć się z jego urządzeniami podrzędnymi. Azure IoT Edge umożliwia konfigurowanie bezpiecznych połączeń między urządzeniami przy użyciu infrastruktury kluczy publicznych (PKI). W takim przypadku zezwalamy urządzeniu podrzędnemu na łączenie się z urządzeniem IoT Edge działającym jako niejawna brama. Aby zachować uzasadnione zabezpieczenia, urządzenie podrzędne powinno potwierdzić tożsamość urządzenia bramy. To sprawdzenie tożsamości uniemożliwia urządzeniom łączenie się z potencjalnie złośliwymi bramami.

Urządzenie podrzędne może być dowolną aplikacją lub platformą, która ma tożsamość utworzoną za pomocą usługi [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) w chmurze. Te aplikacje często używają [zestawu SDK urządzeń Azure IoT](../iot-hub/iot-hub-devguide-sdks.md). Urządzenie podrzędne może nawet być aplikacją działającą na samym urządzeniu bramy IoT Edge. Jednak urządzenie IoT Edge nie może być niższe niż Brama IoT Edge.

Można utworzyć dowolną infrastrukturę certyfikatów, która umożliwia zaufanie wymagane dla topologii bramy urządzeń. W tym artykule przyjęto założenie, że ta sama konfiguracja certyfikatu zostanie użyta do włączenia [zabezpieczeń urzędu certyfikacji x. 509](../iot-hub/iot-hub-x509ca-overview.md) w IoT Hub, co obejmuje certyfikat certyfikatu x. 509 skojarzony z określonym Centrum IoT Hub (główny urząd certyfikacji usługi IoT Hub), szereg certyfikatów podpisanych za pomocą tego urzędu certyfikacji oraz Urząd certyfikacji dla IoT Edge urządzenia.

>[!NOTE]
>*Certyfikat głównego urzędu certyfikacji* używany w tych artykułach odnosi się do urzędu certyfikacji znajdującego się najwyżej w łańcuchu certyfikatów PKI, a nie musi być certyfikatem głównym urzędu certyfikacji. W wielu przypadkach jest to pośredni certyfikat publiczny urzędu certyfikacji.

Poniższe kroki przeprowadzą Cię przez proces tworzenia certyfikatów i instalowania ich w odpowiednich miejscach na bramie. Możesz użyć dowolnej maszyny do wygenerowania certyfikatów, a następnie skopiować je na urządzenie IoT Edge.

## <a name="prerequisites"></a>Wymagania wstępne

Urządzenie z systemem Linux lub Windows z zainstalowanym IoT Edge.

## <a name="set-up-the-device-ca-certificate"></a>Konfigurowanie certyfikatu urzędu certyfikacji urządzenia

Wszystkie bramy IoT Edge muszą mieć zainstalowany certyfikat urzędu certyfikacji urządzenia. Demon Security IoT Edge używa certyfikatu urzędu certyfikacji IoT Edge do podpisywania certyfikatu urzędu certyfikacji obciążenia, co z kolei podpisuje certyfikat serwera dla Centrum IoT Edge. Podczas inicjowania połączenia Brama przedstawia swój certyfikat serwera do urządzenia podrzędnego. Urządzenie podrzędne sprawdza, czy certyfikat serwera jest częścią łańcucha certyfikatów, który jest rzutowany do certyfikatu głównego urzędu certyfikacji. Ten proces umożliwia urządzeniu podrzędnemu potwierdzenie, że brama pochodzi z zaufanego źródła. Aby uzyskać więcej informacji, zobacz [Opis sposobu używania certyfikatów przez Azure IoT Edge](iot-edge-certs.md).

![Konfiguracja certyfikatu bramy](./media/how-to-create-transparent-gateway/gateway-setup.png)

Certyfikat głównego urzędu certyfikacji i certyfikat urzędu certyfikacji urządzenia (z kluczem prywatnym) muszą być obecne na urządzeniu bramy IoT Edge i skonfigurowane w pliku IoT Edge config. YAML. Należy pamiętać, że w tym przypadku *certyfikat głównego urzędu certyfikacji* oznacza urząd certyfikatu znajdujący się najwyżej w tym scenariuszu IoT Edge. Certyfikat urzędu certyfikacji urządzenia bramy i certyfikaty urządzeń podrzędnych muszą być rzutowane do tego samego certyfikatu głównego urzędu certyfikacji.

>[!TIP]
>Proces instalowania certyfikatu głównego urzędu certyfikacji i certyfikatu urzędu certyfikacji na urządzeniu IoT Edge jest również bardziej szczegółowy w temacie [Zarządzanie certyfikatami na urządzeniu IoT Edge](how-to-manage-device-certificates.md).

Przygotuj następujące pliki:

* Certyfikat głównego urzędu certyfikacji
* Certyfikat urzędu certyfikacji urządzenia
* Klucz prywatny urzędu certyfikacji urządzenia

W przypadku scenariuszy produkcyjnych należy generować te pliki przy użyciu własnego urzędu certyfikacji. W przypadku scenariuszy deweloperskich i testowych można użyć certyfikatów demonstracyjnych.

1. Jeśli używasz certyfikatów demonstracyjnych, użyj następującego zestawu kroków, aby utworzyć pliki:
   1. [Utwórz certyfikat głównego urzędu certyfikacji](how-to-create-test-certificates.md#create-root-ca-certificate). Na końcu tych instrukcji będziesz mieć plik certyfikatu głównego urzędu certyfikacji:
      * `<path>/certs/azure-iot-test-only.root.ca.cert.pem`.

   2. [Utwórz certyfikat urzędu certyfikacji urządzenia IoT Edge](how-to-create-test-certificates.md#create-iot-edge-device-ca-certificates). Na końcu tych instrukcji będziesz mieć dwa pliki, certyfikat urzędu certyfikacji urządzenia i jego klucz prywatny:
      * `<path>/certs/iot-edge-device-<cert name>-full-chain.cert.pem`lub
      * `<path>/private/iot-edge-device-<cert name>.key.pem`

2. Jeśli te pliki zostały utworzone na innym komputerze, skopiuj je na urządzenie IoT Edge.

3. Na urządzeniu IoT Edge Otwórz plik konfiguracji demona zabezpieczeń.
   * Systemy`C:\ProgramData\iotedge\config.yaml`
   * System`/etc/iotedge/config.yaml`

4. Znajdź sekcję **Certyfikaty** pliku i podaj identyfikatory URI plików dla trzech plików jako wartości dla następujących właściwości:
   * **device_ca_cert**: certyfikat urzędu certyfikacji urządzenia
   * **device_ca_pk**: klucz prywatny urzędu certyfikacji urządzenia
   * **trusted_ca_certs**: certyfikat głównego urzędu certyfikacji

5. Zapisz i zamknij plik.

6. Uruchom ponownie IoT Edge.
   * Systemy`Restart-Service iotedge`
   * System`sudo systemctl restart iotedge`

## <a name="deploy-edgehub-to-the-gateway"></a>Wdróż edgeHub na bramie

Podczas pierwszej instalacji IoT Edge na urządzeniu zostanie automatycznie uruchomiony tylko jeden moduł systemowy: Agent IoT Edge. Po utworzeniu pierwszego wdrożenia dla urządzenia jest również uruchamiany drugi moduł systemu IoT Edge centrum.

Centrum IoT Edge jest odpowiedzialne za odbieranie komunikatów przychodzących z urządzeń podrzędnych i kierowanie ich do kolejnego miejsca docelowego. Jeśli moduł **edgeHub** nie jest uruchomiony na urządzeniu, Utwórz początkowe wdrożenie dla urządzenia. Wdrożenie będzie wyglądało puste, ponieważ nie dodasz żadnych modułów, ale upewni się, że oba moduły systemowe są uruchomione.

Możesz sprawdzić, które moduły są uruchomione na urządzeniu, sprawdzając jego szczegóły dotyczące urządzeń w Azure Portal, wyświetlając stan urządzenia w programie Visual Studio lub Visual Studio Code lub uruchamiając polecenie `iotedge list` na samym urządzeniu.

Jeśli moduł **edgeAgent** jest uruchomiony bez modułu **edgeHub** , wykonaj następujące czynności:

1. W witrynie Azure Portal przejdź do centrum IoT Hub.

2. Przejdź do **IoT Edge** i wybierz urządzenie IoT Edge, które ma być używane jako brama.

3. Wybierz pozycję **Ustaw moduły**.

4. Wybierz pozycję **Dalej: trasy**.

5. Na stronie **trasy** powinna istnieć trasa domyślna, która wysyła wszystkie wiadomości z modułu lub z urządzenia podrzędnego do IoT Hub. W przeciwnym razie Dodaj nową trasę o następujących wartościach, a następnie wybierz pozycję **Przegląd + Utwórz**:
   * **Nazwa**:`route`
   * **Wartość**:`FROM /messages/* INTO $upstream`

6. Na stronie **Recenzja i tworzenie** wybierz pozycję **Utwórz**.

## <a name="open-ports-on-gateway-device"></a>Otwórz porty na urządzeniu bramy

Standardowe urządzenia IoT Edge nie potrzebują żadnej łączności przychodzącej z funkcją, ponieważ cała komunikacja z IoT Hub odbywa się za pośrednictwem połączeń wychodzących. Urządzenia bramy różnią się, ponieważ muszą odbierać komunikaty z urządzeń podrzędnych. Jeśli Zapora jest między urządzeniami podrzędnymi a urządzeniem bramy, komunikacja musi być również możliwa za pomocą zapory.

Aby scenariusz bramy działał prawidłowo, należy otworzyć co najmniej jeden z obsługiwanych protokołów IoT Edge Hub dla ruchu przychodzącego z urządzeń podrzędnych. Obsługiwane protokoły to MQTT, AMQP, HTTPS, MQTT za pośrednictwem obiektów WebSockets oraz AMQP za pośrednictwem obiektów WebSockets.

| Port | Protokół |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP + WS |

## <a name="route-messages-from-downstream-devices"></a>Kierowanie komunikatów z urządzeń podrzędnych

Środowisko uruchomieniowe IoT Edge może kierować komunikaty wysyłane z urządzeń podrzędnych podobnie jak komunikaty wysyłane przez moduły. Ta funkcja umożliwia wykonywanie analiz w module uruchomionym w bramie przed wysłaniem danych do chmury.

Obecnie sposób rozsyłania komunikatów wysyłanych przez urządzenia podrzędne polega na rozróżnieniu ich od komunikatów wysyłanych przez moduły. Komunikaty wysyłane przez moduły zawierają Właściwość systemową o nazwie **connectionModuleId** , ale komunikaty wysyłane przez urządzenia podrzędne nie są obsługiwane. Można użyć klauzuli WHERE trasy, aby wykluczyć wszystkie komunikaty zawierające tę właściwość System.

Poniższa trasa to przykład, który wyśle komunikaty z dowolnego urządzenia podrzędnego do modułu o nazwie `ai_insights` , a następnie z `ai_insights` do IoT Hub.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")",
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream"
    }
}
```

Aby uzyskać więcej informacji na temat routingu wiadomości, zobacz [wdrażanie modułów i ustanawianie tras](./module-composition.md#declare-routes).

## <a name="enable-extended-offline-operation"></a>Włącz rozszerzoną operację offline

Począwszy od [wersji 1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) środowiska uruchomieniowego IoT Edge, urządzenie bramy i urządzenia podrzędne łączące się z nim można skonfigurować do przedłużonej operacji w trybie offline.

Dzięki tej możliwości lokalne moduły lub urządzenia podrzędne mogą ponownie uwierzytelniać się przy użyciu urządzenia z IoT Edge w razie konieczności i komunikować się ze sobą za pomocą komunikatów i metod nawet w przypadku odłączenia od centrum IoT Hub. Aby uzyskać więcej informacji, zobacz informacje o [rozszerzonych możliwościach trybu offline dla urządzeń IoT Edge, modułów i urządzeń podrzędnych](offline-capabilities.md).

Aby włączyć rozszerzone możliwości trybu offline, należy ustanowić relację nadrzędny-podrzędny między urządzeniem bramy IoT Edge a urządzeniami podrzędnymi, które będą się z nim połączyć. Te kroki zostały omówione bardziej szczegółowo w następnym artykule tej serii, [uwierzytelniaj urządzenie podrzędne w usłudze Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz już skonfigurowane urządzenie IoT Edge jako niejawną bramę, musisz skonfigurować urządzenia podrzędne, aby ufać bramie i wysyłać do niej komunikaty. Kontynuuj, aby [uwierzytelnić urządzenie podrzędne w usłudze Azure IoT Hub](how-to-authenticate-downstream-device.md) w celu wykonania następnych kroków w celu skonfigurowania niejawnego scenariusza bramy.
