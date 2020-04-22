---
title: Tworzenie przezroczystego urządzenia bramy — usługa Azure IoT Edge | Dokumenty firmy Microsoft
description: Używanie urządzenia usługi Azure IoT Edge jako przezroczystej bramy, która może przetwarzać informacje z urządzeń podrzędnych
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/03/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: e563e67b5e951b43e5782f8c845c8ec46ff3e9bb
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687162"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Konfigurowanie urządzenia usługi IoT Edge, aby działało jako przezroczysta brama

Ten artykuł zawiera szczegółowe instrukcje konfigurowania urządzenia usługi IoT Edge do działania jako przezroczysta brama dla innych urządzeń do komunikowania się z Centrum IoT Hub. W tym artykule użyto terminu *Brama usługi IoT Edge* w odniesieniu do urządzenia usługi IoT Edge skonfigurowanego jako brama przezroczysta. Aby uzyskać więcej informacji, zobacz [Jak urządzenie Usługi IoT Edge może być używane jako brama](./iot-edge-as-gateway.md).

>[!NOTE]
>Obecnie:
>
> * Urządzenia z obsługą krawędzi nie mogą łączyć się z bramami usługi IoT Edge.
> * Urządzenia podrzędne nie mogą używać przekazywania plików.

Istnieją trzy ogólne kroki konfigurowania pomyślnego połączenia bramy przezroczystej. Ten artykuł obejmuje pierwszy krok:

1. **Urządzenie bramy musi mieć możliwość bezpiecznego łączenia się z urządzeniami podrzędnymi, odbierania komunikacji z urządzeń podrzędnych i kierowania wiadomości do właściwego miejsca docelowego.**
2. Urządzenie podrzędne musi mieć tożsamość urządzenia, aby móc uwierzytelniać się za pomocą usługi IoT Hub i wiedzieć, aby komunikować się za pośrednictwem urządzenia bramy. Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie urządzenia podrzędnego w centrum Usługi Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. Urządzenie podrzędne musi bezpiecznie połączyć się z urządzeniem bramy. Aby uzyskać więcej informacji, zobacz [Łączenie urządzenia podrzędnego z bramą usługi Azure IoT Edge](how-to-connect-downstream-device.md).

Aby urządzenie działało jako brama, musi mieć możliwość bezpiecznego łączenia się z jego urządzeniami podrzędnymi. Usługa Azure IoT Edge umożliwia konfigurowanie bezpiecznych połączeń między urządzeniami za pomocą infrastruktury kluczy publicznych (PKI). W takim przypadku zezwalamy urządzeniu podrzędnemu na łączenie się z urządzeniem usługi IoT Edge działającym jako przezroczysta brama. Aby zachować odpowiednie bezpieczeństwo, urządzenie podrzędne powinno potwierdzić tożsamość urządzenia bramy. Ta kontrola tożsamości uniemożliwia urządzeniom łączenie się z potencjalnie złośliwymi bramami.

Urządzenie podrzędne w scenariuszu bramy przezroczystej może być dowolną aplikacją lub platformą, która ma tożsamość utworzoną za pomocą usługi w chmurze [usługi Azure IoT Hub.](https://docs.microsoft.com/azure/iot-hub) W wielu przypadkach te aplikacje używają [SDK urządzenia Usługi Azure IoT.](../iot-hub/iot-hub-devguide-sdks.md) Ze wszystkich praktycznych względów urządzenie podrzędne może być nawet aplikacją działającą na samym urządzeniu bramy usługi IoT Edge. Jednak urządzenie Usługi IoT Edge nie może znajdować się za bramą usługi IoT Edge.

Można utworzyć dowolną infrastrukturę certyfikatów, która umożliwia zaufanie wymagane dla topologii bramy urządzenia. W tym artykule zakładamy tę samą konfigurację certyfikatu, której użyjesz do włączenia [zabezpieczeń urzędu certyfikacji X.509](../iot-hub/iot-hub-x509ca-overview.md) w centrum IoT Hub, która obejmuje certyfikat urzędu certyfikacji X.509 skojarzony z określonym centrum IoT hub (głównym urzędem certyfikacji IoT), serię certyfikatów podpisanych z tym urzędem certyfikacji i urząd certyfikacji dla urządzenia Usługi IoT Edge.

![Konfiguracja certyfikatu bramy](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>Termin "główny urząd certyfikacji" używany w tym artykule odnosi się do najwyższego certyfikatu urzędu publicznego łańcucha certyfikatów infrastruktury kluczy publicznych, a niekoniecznie do katalogu głównego certyfikatu syndykatu urzędu certyfikacji. W wielu przypadkach jest to w rzeczywistości pośredni certyfikat publiczny urzędu certyfikacji.

Demon zabezpieczeń usługi IoT Edge używa certyfikatu urzędu certyfikacji urządzenia usługi IoT Edge do podpisywania certyfikatu urzędu certyfikacji obciążenia, który z kolei podpisuje certyfikat serwera dla centrum Usługi IoT Edge. Brama przedstawia swój certyfikat serwera do urządzenia podrzędnego podczas inicjowania połączenia. Urządzenie podrzędne sprawdza, czy certyfikat serwera jest częścią łańcucha certyfikatów, który jest przekażnie do głównego certyfikatu urzędu certyfikacji. Ten proces umożliwia urządzeniu podrzędnemu potwierdzenie, że brama pochodzi z zaufanego źródła. Aby uzyskać więcej informacji, zobacz [Opis sposobu używania certyfikatów przez usługę Azure IoT Edge.](iot-edge-certs.md)

Poniższe kroki przebiegają przez proces tworzenia certyfikatów i instalowania ich we właściwych miejscach na bramie. Do wygenerowania certyfikatów można użyć dowolnego komputera, a następnie skopiować je na urządzenie usługi IoT Edge.

## <a name="prerequisites"></a>Wymagania wstępne

Urządzenie usługi Azure IoT Edge skonfigurowane z [certyfikatami produkcyjnymi](how-to-manage-device-certificates.md).

## <a name="deploy-edgehub-to-the-gateway"></a>Wdrażanie usługi edgeHub w bramie

Podczas pierwszej instalacji usługi IoT Edge na urządzeniu automatycznie uruchamia się tylko jeden moduł systemowy: agent usługi IoT Edge. Po utworzeniu pierwszego wdrożenia więcej urządzenia, drugi moduł systemowy, Centrum Usługi IoT Edge, jest również uruchamiany.

Usługa IoT Edge hub jest odpowiedzialny za odbieranie wiadomości przychodzących z urządzeń podrzędnych i routingu ich do następnego miejsca docelowego. Jeśli moduł **edgeHub** nie jest uruchomiony na urządzeniu, utwórz wdrożenie początkowe dla urządzenia. Wdrożenie będzie wyglądać puste, ponieważ nie dodać żadnych modułów, ale upewnij się, że oba moduły systemowe są uruchomione.

Można sprawdzić, które moduły są uruchomione na urządzeniu, sprawdzając jego szczegóły urządzenia w witrynie Azure portal, wyświetlanie `iotedge list` stanu urządzenia w programie Visual Studio lub Visual Studio Code lub uruchamiając polecenie na samym urządzeniu.

Jeśli moduł **edgeAgent** działa bez modułu **edgeHub,** należy wykonać następujące kroki:

1. W witrynie Azure Portal przejdź do centrum IoT Hub.

2. Przejdź do **usługi IoT Edge** i wybierz urządzenie Usługi IoT Edge, którego chcesz użyć jako bramy.

3. Wybierz pozycję **Ustaw moduły**.

4. Wybierz opcję **Dalej**.

5. Na stronie **Określ trasy** powinna być domyślna trasa, która wysyła wszystkie wiadomości ze wszystkich modułów do Centrum IoT Hub. Jeśli tak nie jest, dodaj następujący kod, a następnie wybierz przycisk **Dalej**.

   ```JSON
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

6. Na stronie **Szablon recenzji** wybierz pozycję **Prześlij**.

## <a name="open-ports-on-gateway-device"></a>Otwieranie portów na urządzeniu bramy

Standardowe urządzenia Usługi IoT Edge nie potrzebują żadnej łączności przychodzącej do działania, ponieważ cała komunikacja z centrum IoT Hub odbywa się za pośrednictwem połączeń wychodzących. Urządzenia bramy są różne, ponieważ muszą odbierać wiadomości z ich urządzeń podrzędnych. Jeśli zapora znajduje się między urządzeniami podrzędnymi a urządzeniem bramy, komunikacja musi być również możliwa za pośrednictwem zapory.

Aby scenariusz bramy działał, co najmniej jeden z obsługiwanych protokołów usługi IoT Edge musi być otwarty dla ruchu przychodzącego z urządzeń podrzędnych. Obsługiwane protokoły to MQTT, AMQP, HTTPS, MQTT over WebSockets i AMQP over WebSockets.

| Port | Protocol (Protokół) |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS |

## <a name="route-messages-from-downstream-devices"></a>Rozsyłanie wiadomości z urządzeń podrzędnych

Środowisko wykonawcze IoT Edge może rozsyłać wiadomości wysyłane z urządzeń podrzędnych, podobnie jak wiadomości wysyłane przez moduły. Ta funkcja umożliwia wykonywanie analizy w module uruchomionym na bramie przed wysłaniem jakichkolwiek danych do chmury.

Obecnie sposób, w jaki rozsyłasz wiadomości wysyłane przez urządzenia podrzędne, polega na odróżnieniu ich od wiadomości wysyłanych przez moduły. Wszystkie komunikaty wysyłane przez moduły zawierają właściwość systemową o nazwie **connectionModuleId,** ale wiadomości wysyłane przez urządzenia podrzędne nie. Klauzula WHERE trasy służy do wykluczania wszelkich komunikatów, które zawierają tę właściwość systemu.

Poniższa trasa jest przykładem, który wysyłałby wiadomości z `ai_insights`dowolnego urządzenia `ai_insights` podrzędnego do modułu o nazwie , a następnie z centrum IoT Hub.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")",
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream"
    }
}
```

Aby uzyskać więcej informacji na temat routingu wiadomości, zobacz [Wdrażanie modułów i ustanawianie tras](./module-composition.md#declare-routes).

## <a name="enable-extended-offline-operation"></a>Włącz rozszerzoną obsługę w trybie offline

Począwszy od [wersji 1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) środowiska wykonawczego usługi IoT Edge, urządzenie bramy i urządzenia podrzędne łączące się z nim można skonfigurować do rozszerzonej pracy w trybie offline.

Dzięki tej funkcji moduły lokalne lub urządzenia podrzędne mogą ponownie uwierzytelniać się za pomocą urządzenia usługi IoT Edge w razie potrzeby i komunikować się ze sobą za pomocą wiadomości i metod, nawet po odłączeniu od centrum IoT hub. Aby uzyskać więcej informacji, zobacz [Opis rozszerzonych funkcji offline dla urządzeń, modułów i urządzeń podrzędnych usługi IoT Edge.](offline-capabilities.md)

Aby włączyć rozszerzone możliwości w trybie offline, należy ustanowić relację nadrzędny-podrzędny między urządzeniem bramy usługi IoT Edge a urządzeniami podrzędnymi, które będą się z nim łączyć. Te kroki są wyjaśnione bardziej szczegółowo w [uwierzytelnij urządzenie podrzędne do usługi Azure IoT Hub.](how-to-authenticate-downstream-device.md)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz urządzenie usługi IoT Edge działające jako przezroczysta brama, musisz skonfigurować urządzenia podrzędne, aby ufały bramie i wysyłały do niej wiadomości. Kontynuuj [uwierzytelnienie urządzenia podrzędnego do usługi Azure IoT Hub, aby](how-to-authenticate-downstream-device.md) uzyskać kolejne kroki konfigurowania scenariusza bramy przezroczystej.
