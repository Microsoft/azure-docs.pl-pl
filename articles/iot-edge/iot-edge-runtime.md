---
title: Dowiedz się, jak środowisko uruchomieniowe zarządza urządzeniami — Azure IoT Edge | Microsoft Docs
description: Dowiedz się, jak środowisko uruchomieniowe IoT Edge zarządza modułami, zabezpieczeniami, komunikacją i raportowaniem na urządzeniach
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: amqp, mqtt, devx-track-csharp
ms.openlocfilehash: 25493312854bbd495dce01f8f107b3e3320cb92c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89016958"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Poznaj środowisko uruchomieniowe Azure IoT Edge i jego architekturę

Środowisko uruchomieniowe usługi IoT Edge to kolekcja programów, które zmieniają urządzenie w urządzenie usługi IoT Edge. Zbiorowo składniki środowiska uruchomieniowego IoT Edge umożliwiają IoT Edge urządzeniom otrzymywanie kodu do uruchomienia na brzegu i przekazywanie wyników.

Środowisko uruchomieniowe IoT Edge jest odpowiedzialne za następujące funkcje na urządzeniach IoT Edge:

* Instaluje i aktualizuje pakiety robocze na urządzeniu.
* Utrzymuje standardy zabezpieczeń usługi Azure IoT Edge na urządzeniu.
* Upewnij się, że [moduły IoT Edge](iot-edge-modules.md) są zawsze uruchomione.
* Przesyła raporty o kondycji modułów do chmury na potrzeby zdalnego monitorowania.
* Zarządzanie komunikacją między urządzeniami podrzędnymi a urządzeniami IoT Edge.
* Zarządzanie komunikacją między modułami na urządzeniu IoT Edge.
* Zarządzanie komunikacją między urządzeniem IoT Edge i chmurą.

![Środowisko uruchomieniowe komunikuje się ze szczegółowymi informacjami i kondycją modułu, aby IoT Hub](./media/iot-edge-runtime/Pipeline.png)

Obowiązki środowiska uruchomieniowego IoT Edge dzielą się na dwie kategorie: komunikacja i zarządzanie modułem. Te dwie role są wykonywane przez dwa składniki, które są częścią środowiska uruchomieniowego IoT Edge.*Centrum IoT Edge* jest odpowiedzialne za komunikację, a *Agent IoT Edge* wdraża i monitoruje moduły.

Zarówno centrum IoT Edge, jak i Agent IoT Edge są modułami, podobnie jak każdy inny moduł uruchomiony na IoT Edge urządzeniu. Są one czasami określane jako *moduły środowiska uruchomieniowego*.

## <a name="iot-edge-hub"></a>IoT Edge Hub

IoT Edge Hub to jeden z dwóch modułów, które tworzą Azure IoT Edge środowiska uruchomieniowego. Działa jako lokalny serwer proxy dla IoT Hub, uwidaczniając te same punkty końcowe protokołu co IoT Hub. Taka spójność oznacza, że klienci (urządzenia lub moduły) mogą łączyć się z IoT Edge środowiska uruchomieniowego w taki sam sposób jak IoT Hub.

>[!NOTE]
> IoT Edge Hub obsługuje klientów, którzy łączą się za pomocą MQTT lub AMQP. Nie obsługuje klientów korzystających z protokołu HTTP.

Centrum IoT Edge nie jest pełną wersją IoT Hub uruchomioną lokalnie. IoT Edge Hub dyskretnie delegowanie niektórych zadań do IoT Hub. Na przykład IoT Edge centrum przekazuje żądania uwierzytelniania do IoT Hub, gdy urządzenie próbuje nawiązać połączenie. Po ustanowieniu pierwszego połączenia informacje o zabezpieczeniach są buforowane lokalnie przez IoT Edge centrum. Przyszłe połączenia z tego urządzenia są dozwolone bez konieczności ponownego uwierzytelniania w chmurze.

Aby zmniejszyć przepustowość używaną przez rozwiązanie IoT Edge, centrum IoT Edge optymalizuje, ile rzeczywistych połączeń odbywa się w chmurze. Usługa IoT Edge Hub pobiera logiczne połączenia z modułów lub urządzeń podrzędnych oraz łączy je z jednym połączeniem fizycznym z chmurą. Szczegóły tego procesu są niewidoczne dla reszty rozwiązania. Klienci uważają swoje własne połączenie z chmurą, nawet jeśli są wysyłane przez to samo połączenie.

![IoT Edge Hub jest bramą między urządzeniami fizycznymi i IoT Hub](./media/iot-edge-runtime/Gateway.png)

IoT Edge Hub może określić, czy jest on połączony z IoT Hub. W przypadku utraty połączenia IoT Edge centrum zapisuje na komputerze aktualizacje lub sznurki. Po ponownym nawiązaniu połączenia synchronizuje wszystkie dane. Lokalizacja używana dla tymczasowej pamięci podręcznej jest określana przez właściwość sznurka modułu IoT Edge Hub. Rozmiar pamięci podręcznej nie jest nieograniczony i zostanie powiększony, dopóki urządzenie ma pojemność magazynu.Aby uzyskać więcej informacji, zobacz [możliwości trybu offline](offline-capabilities.md).

### <a name="module-communication"></a>Komunikacja modułu

IoT Edge Hub ułatwia komunikację modułu z modułem. Używanie Centrum IoT Edge jako brokera komunikatów zachowuje moduły niezależne od siebie. Moduły muszą określać dane wejściowe, na których są akceptowane wiadomości i dane wyjściowe, do których są zapisywane wiadomości. Programista rozwiązań może połączyć te dane wejściowe i dane wyjściowe, aby moduły przetwarzali danych w kolejności specyficznej dla tego rozwiązania.

![IoT Edge Hub ułatwia komunikację między modułami](./media/iot-edge-runtime/module-endpoints.png)

Aby wysłać dane do centrum IoT Edge, moduł wywołuje metodę SendEventAsync. Pierwszy argument określa, w którym wyniku chcesz wysłać wiadomość. Następujący pseudokodzie wysyła komunikat w **output1**:

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings);
   await client.OpenAsync();
   await client.SendEventAsync("output1", message);
   ```

Aby odebrać komunikat, zarejestruj wywołanie zwrotne, które przetwarza wiadomości przychodzące do określonych danych wejściowych. Następujący pseudokodzie rejestruje funkcję messageProcessor, która ma być używana do przetwarzania wszystkich komunikatów odebranych w **INPUT1**:

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

Aby uzyskać więcej informacji na temat klasy ModuleClient i jej metod komunikacji, zobacz Dokumentacja interfejsu API dla preferowanego języka SDK: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)lub [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

Deweloper rozwiązania jest odpowiedzialny za określenie reguł, które określają, jak centrum IoT Edge przekazuje komunikaty między modułami. Reguły routingu są zdefiniowane w chmurze i wypychane do IoT Edge Hub w swoim sznurze modułu. Ta sama składnia dla IoT Hub Routes służy do definiowania tras między modułami w Azure IoT Edge. Aby uzyskać więcej informacji, zobacz [Informacje o sposobie wdrażania modułów i ustanawiania tras w programie IoT Edge](module-composition.md).

![Trasy między modułami przechodzą przez Centrum IoT Edge](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>Agent IoT Edge

Agent IoT Edge jest innym modułem, który tworzy Azure IoT Edge środowiska uruchomieniowego. Jest on odpowiedzialny za tworzenie wystąpień modułów, zapewnienie, że nadal działają, i raportowanie stanu modułów z powrotem do IoT Hub. Te dane konfiguracyjne są zapisywane jako właściwości sznurka modułu agenta IoT Edge.

[Demon zabezpieczenia IoT Edge](iot-edge-security-manager.md) uruchamia agenta IoT Edge przy uruchamianiu urządzenia. Agent pobiera sznurek modułu z IoT Hub i sprawdza manifest wdrożenia. Manifest wdrożenia to plik JSON, który deklaruje moduły, które muszą zostać uruchomione.

Każdy element w manifeście wdrożenia zawiera określone informacje o module i jest używany przez agenta IoT Edge do kontrolowania cyklu życia modułu. Niektóre z bardziej interesujących właściwości są następujące:

* **Settings. Image** — obraz kontenera, którego używa Agent IoT Edge do uruchomienia modułu. Jeśli obraz jest chroniony hasłem, Agent IoT Edge musi być skonfigurowany przy użyciu poświadczeń dla rejestru kontenerów. Poświadczenia dla rejestru kontenerów można skonfigurować zdalnie przy użyciu manifestu wdrażania lub na IoT Edge samym urządzeniu przez zaktualizowanie `config.yaml` pliku w folderze programu IoT Edge.
* **Settings. SetOptions** — ciąg, który jest przesyłany bezpośrednio do demona kontenera Moby podczas uruchamiania kontenera modułu. Dodanie opcji w tej właściwości pozwala na zaawansowane konfiguracje, takie jak przekazywanie portów lub instalowanie woluminów do kontenera modułu.  
* **status** — stan, w którym Agent IoT Edge umieszcza moduł. Zazwyczaj ta wartość jest ustawiana jako *uruchomiona* , gdy większość osób chce, aby Agent IoT Edge natychmiast uruchomił wszystkie moduły na urządzeniu. Można jednak określić początkowy stan modułu, który ma zostać zatrzymany, i poczekać na przyszły czas, aby poinformować IoT Edge agenta o konieczności uruchomienia modułu.Agent IoT Edge raportuje stan każdego modułu z powrotem do chmury w raportowanych właściwościach. Różnica między żądaną właściwością a raportowaną właściwością jest wskaźnikiem urządzenia błędna. Obsługiwane są następujące stany:

  * Trwa
  * Uruchomienie
  * Nieprawidłowy
  * Niepowodzenie
  * Zatrzymano

* **restartPolicy** — sposób ponownego uruchomienia modułu przez agenta IoT Edge. Możliwe wartości to:
  
  * `never` — Agent IoT Edge nigdy nie uruchamia ponownie modułu.
  * `on-failure` — Jeśli moduł ulegnie awarii, Agent IoT Edge ponownie go uruchomi. Jeśli moduł nie zostanie prawidłowo zamknięty, Agent IoT Edge nie uruchomi go ponownie.
  * `on-unhealthy` — Jeśli moduł ulegnie awarii lub jest uznawany za nieprawidłowy, Agent IoT Edge ponownie go uruchomi.
  * `always` — Jeśli moduł ulegnie awarii, jest uznawany za w złej kondycji lub zamknięty w dowolny sposób, Agent IoT Edge ponownie go uruchomi.

* **imagePullPolicy** — czy Agent IoT Edge próbuje pobrać najnowszy obraz dla modułu automatycznie. Jeśli nie określisz wartości, wartość domyślna to *OnCreate*. Możliwe wartości to:

  * `on-create` — Podczas uruchamiania modułu lub aktualizowania modułu na podstawie nowego manifestu wdrożenia Agent IoT Edge podejmie próbę ściągnięcia obrazu modułu z rejestru kontenerów.
  * `never` -Agent IoT Edge nigdy nie podejmie próby ściągnięcia obrazu modułu z rejestru kontenerów. W przypadku tej konfiguracji użytkownik jest odpowiedzialny za pobieranie obrazu modułu na urządzenie i zarządzanie wszelkimi aktualizacjami obrazu.

Agent IoT Edge wysyła odpowiedź środowiska uruchomieniowego do IoT Hub. Poniżej znajduje się lista możliwych odpowiedzi:
  
* 200 — OK
* 400 — konfiguracja wdrożenia jest źle sformułowana lub nieprawidłowa.
* 417 — urządzenie nie ma ustawionej konfiguracji wdrożenia.
* 412 — wersja schematu w konfiguracji wdrożenia jest nieprawidłowa.
* 406 — urządzenie IoT Edge jest w trybie offline lub nie wysyła raportów o stanie.
* 500 — Wystąpił błąd w czasie wykonywania IoT Edge.

Aby uzyskać więcej informacji, zobacz [Informacje o sposobie wdrażania modułów i ustanawiania tras w programie IoT Edge](module-composition.md).

### <a name="security"></a>Zabezpieczenia

Agent IoT Edge odgrywa kluczową rolę w zabezpieczeniach IoT Edge urządzeniu. Na przykład wykonuje akcje takie jak sprawdzenie obrazu modułu przed jego uruchomieniem.

Aby uzyskać więcej informacji na temat środowiska zabezpieczeń Azure IoT Edge, Przeczytaj o programie [IoT Edge Security Manager](iot-edge-security-manager.md).

## <a name="next-steps"></a>Następne kroki

[Omówienie modułów usługi Azure IoT Edge](iot-edge-modules.md)
