---
title: Tworzenie przezroczystego urządzenia bramy — Azure IoT Edge | Microsoft Docs
description: Używanie urządzenia Azure IoT Edge jako przezroczystej bramy, która może przetwarzać informacje z urządzeń podrzędnych
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/12/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: ae01fc2ef8761305c2096904471ce75b69d1150d
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92048410"
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

Urządzenie podrzędne może być dowolną aplikacją lub platformą, która ma tożsamość utworzoną za pomocą usługi [Azure IoT Hub](../iot-hub/index.yml) w chmurze. Te aplikacje często używają [zestawu SDK urządzeń Azure IoT](../iot-hub/iot-hub-devguide-sdks.md). Urządzenie podrzędne może nawet być aplikacją działającą na samym urządzeniu bramy IoT Edge. Jednak urządzenie IoT Edge nie może być niższe niż Brama IoT Edge.

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
      * `<path>/certs/iot-edge-device-<cert name>-full-chain.cert.pem` lub
      * `<path>/private/iot-edge-device-<cert name>.key.pem`

2. Jeśli te pliki zostały utworzone na innym komputerze, skopiuj je na urządzenie IoT Edge.

3. Na urządzeniu IoT Edge Otwórz plik konfiguracji demona zabezpieczeń.
   * Systemy `C:\ProgramData\iotedge\config.yaml`
   * System `/etc/iotedge/config.yaml`

4. Znajdź sekcję **Certyfikaty** pliku i podaj identyfikatory URI plików dla trzech plików jako wartości dla następujących właściwości:
   * **device_ca_cert**: certyfikat urzędu certyfikacji urządzenia
   * **device_ca_pk**: klucz prywatny urzędu certyfikacji urządzenia
   * **trusted_ca_certs**: certyfikat głównego urzędu certyfikacji

5. Zapisz i zamknij plik.

6. Uruchom ponownie IoT Edge.
   * Systemy `Restart-Service iotedge`
   * System `sudo systemctl restart iotedge`

## <a name="deploy-edgehub-and-route-messages"></a>Wdrażanie edgeHub i komunikatów routingu

Urządzenia podrzędne wysyłają dane telemetryczne i komunikaty do urządzenia bramy, gdzie moduł IoT Edge Hub jest odpowiedzialny za kierowanie informacji do innych modułów lub IoT Hub. Aby przygotować urządzenie bramy do tej funkcji, upewnij się, że:

* Moduł IoT Edge Hub został wdrożony na urządzeniu.

  Podczas pierwszej instalacji IoT Edge na urządzeniu zostanie automatycznie uruchomiony tylko jeden moduł systemowy: Agent IoT Edge. Po utworzeniu pierwszego wdrożenia dla urządzenia jest również uruchamiany drugi moduł systemowy IoT Edge centrum. Jeśli moduł **edgeHub** nie jest uruchomiony na urządzeniu, Utwórz wdrożenie dla tego urządzenia.

* Moduł IoT Edge Hub ma skonfigurowane trasy do obsługi komunikatów przychodzących z urządzeń podrzędnych.

  Urządzenie bramy musi mieć trasę w miejscu do obsługi komunikatów z urządzeń podrzędnych lub komunikaty te nie zostaną przetworzone. Komunikaty można wysyłać do modułów na urządzeniu bramy lub bezpośrednio do IoT Hub.

Aby wdrożyć moduł IoT Edge Hub i skonfigurować go przy użyciu tras do obsługi komunikatów przychodzących z urządzeń podrzędnych, wykonaj następujące kroki:

1. W witrynie Azure Portal przejdź do centrum IoT Hub.

2. Przejdź do **IoT Edge** i wybierz urządzenie IoT Edge, które ma być używane jako brama.

3. Wybierz pozycję **Ustaw moduły**.

4. Na stronie **moduły** można dodać wszystkie moduły, które mają zostać wdrożone na urządzeniu bramy. Na potrzeby tego artykułu firma Microsoft koncentruje się na konfigurowaniu i wdrażaniu modułu edgeHub, który nie musi być jawnie ustawiony na tej stronie.

5. Wybierz pozycję **Dalej: trasy**.

6. Na stronie **trasy** upewnij się, że istnieje trasa do obsługi komunikatów pochodzących z urządzeń podrzędnych. Na przykład:

   * Trasa, która wysyła wszystkie wiadomości, niezależnie od modułu lub z urządzenia podrzędnego, do IoT Hub:
       * **Nazwa**: `allMessagesToHub`
       * **Wartość**: `FROM /messages/* INTO $upstream`

   * Trasa, która wysyła wszystkie komunikaty ze wszystkich urządzeń podrzędnych do IoT Hub:
      * **Nazwa**: `allDownstreamToHub`
      * **Wartość**: `FROM /messages/* WHERE NOT IS_DEFINED ($connectionModuleId) INTO $upstream`

      Ta trasa działa, ponieważ, w przeciwieństwie do komunikatów z modułów IoT Edge, komunikaty z urządzeń podrzędnych nie mają skojarzonego z nimi identyfikatora modułu. Użycie klauzuli **WHERE** trasy pozwala nam odfiltrować wszystkie komunikaty z tą właściwością systemu.

      Aby uzyskać więcej informacji na temat routingu wiadomości, zobacz [wdrażanie modułów i ustanawianie tras](./module-composition.md#declare-routes).

7. Po utworzeniu trasy lub tras wybierz pozycję **Przegląd + Utwórz**.

8. Na stronie **Recenzja i tworzenie** wybierz pozycję **Utwórz**.

## <a name="open-ports-on-gateway-device"></a>Otwórz porty na urządzeniu bramy

Standardowe urządzenia IoT Edge nie potrzebują żadnej łączności przychodzącej z funkcją, ponieważ cała komunikacja z IoT Hub odbywa się za pośrednictwem połączeń wychodzących. Urządzenia bramy różnią się, ponieważ muszą odbierać komunikaty z urządzeń podrzędnych. Jeśli Zapora jest między urządzeniami podrzędnymi a urządzeniem bramy, komunikacja musi być również możliwa za pomocą zapory.

Aby scenariusz bramy działał prawidłowo, należy otworzyć co najmniej jeden z obsługiwanych protokołów IoT Edge Hub dla ruchu przychodzącego z urządzeń podrzędnych. Obsługiwane protokoły to MQTT, AMQP, HTTPS, MQTT za pośrednictwem obiektów WebSockets oraz AMQP za pośrednictwem obiektów WebSockets.

| Port | Protokół |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP + WS |

## <a name="enable-extended-offline-operation"></a>Włącz rozszerzoną operację offline

Począwszy od [wersji 1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) środowiska uruchomieniowego IoT Edge, urządzenie bramy i urządzenia podrzędne łączące się z nim można skonfigurować do przedłużonej operacji w trybie offline.

Dzięki tej możliwości lokalne moduły lub urządzenia podrzędne mogą ponownie uwierzytelniać się przy użyciu urządzenia z IoT Edge w razie konieczności i komunikować się ze sobą za pomocą komunikatów i metod nawet w przypadku odłączenia od centrum IoT Hub. Aby uzyskać więcej informacji, zobacz informacje o [rozszerzonych możliwościach trybu offline dla urządzeń IoT Edge, modułów i urządzeń podrzędnych](offline-capabilities.md).

Aby włączyć rozszerzone możliwości trybu offline, należy ustanowić relację nadrzędny-podrzędny między urządzeniem bramy IoT Edge a urządzeniami podrzędnymi, które będą się z nim połączyć. Te kroki zostały omówione bardziej szczegółowo w następnym artykule tej serii, [uwierzytelniaj urządzenie podrzędne w usłudze Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz już skonfigurowane urządzenie IoT Edge jako niejawną bramę, musisz skonfigurować urządzenia podrzędne, aby ufać bramie i wysyłać do niej komunikaty. Kontynuuj, aby [uwierzytelnić urządzenie podrzędne w usłudze Azure IoT Hub](how-to-authenticate-downstream-device.md) w celu wykonania następnych kroków w celu skonfigurowania niejawnego scenariusza bramy.