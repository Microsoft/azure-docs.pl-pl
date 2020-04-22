---
title: Dowiedz się, jak środowisko wykonawcze zarządza urządzeniami — usługa Azure IoT Edge | Dokumenty firmy Microsoft
description: Dowiedz się, jak środowisko wykonawcze Usługi IoT Edge zarządza modułami, zabezpieczeniami, komunikacją i raportowaniem na urządzeniach
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: ef31bd74c73aa081c32031b71392f69a1ca14f75
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730905"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Opis środowiska wykonawczego usługi Azure IoT Edge i jego architektury

Środowisko uruchomieniowe usługi IoT Edge to kolekcja programów, które zmieniają urządzenie w urządzenie usługi IoT Edge. Łącznie składniki środowiska wykonawczego IoT Edge umożliwiają urządzeniom IoT Edge odbieranie kodu do uruchamiania na krawędzi i przekazywanie wyników.

Środowisko wykonawcze IoT Edge jest odpowiedzialne za następujące funkcje na urządzeniach IoT Edge:

* Instaluje i aktualizuje pakiety robocze na urządzeniu.
* Utrzymuje standardy zabezpieczeń usługi Azure IoT Edge na urządzeniu.
* Upewnij się, że [moduły IoT Edge](iot-edge-modules.md) są zawsze uruchomione.
* Przesyła raporty o kondycji modułów do chmury na potrzeby zdalnego monitorowania.
* Zarządzanie komunikacją między urządzeniami podrzędnymi a urządzeniami usługi IoT Edge.
* Zarządzanie komunikacją między modułami na urządzeniu Usługi IoT Edge.
* Zarządzanie komunikacją między urządzeniem Usługi IoT Edge a chmurą.

![Środowisko wykonawcze komunikuje szczegółowe informacje i kondycję modułu z Centrum IoT](./media/iot-edge-runtime/Pipeline.png)

Obowiązki środowiska wykonawczego usługi IoT Edge dzielą się na dwie kategorie: komunikacja i zarządzanie modułami. Te dwie role są wykonywane przez dwa składniki, które są częścią środowiska wykonawczego usługi IoT Edge.*Centrum usługi IoT Edge* jest odpowiedzialny za komunikację, podczas gdy agent usługi *IoT Edge* wdraża i monitoruje moduły.

Zarówno centrum usługi IoT Edge, jak i agent usługi IoT Edge są modułami, podobnie jak każdy inny moduł uruchomiony na urządzeniu Usługi IoT Edge. Są one czasami określane jako *moduły środowiska wykonawczego*.

## <a name="iot-edge-hub"></a>Koncentrator usługi IoT Edge

Centrum usługi IoT Edge jest jednym z dwóch modułów, które tworzą środowisko uruchomieniowe usługi Azure IoT Edge. Działa jako lokalny serwer proxy dla Usługi IoT Hub, ujawniając te same punkty końcowe protokołu co Centrum IoT Hub. Ta spójność oznacza, że klienci (czy urządzenia lub moduły) można połączyć się ze środowiska wykonawczego usługi IoT Edge, tak jak w przypadku usługi IoT Hub.

>[!NOTE]
> Centrum IoT Edge obsługuje klientów, którzy łączą się przy użyciu MQTT lub AMQP. Nie obsługuje klientów korzystających z protokołu HTTP.

Centrum usługi IoT Edge nie jest pełną wersją usługi IoT Hub działającej lokalnie. Centrum usługi IoT Edge po cichu deleguje niektóre zadania do usługi IoT Hub. Na przykład centrum usługi IoT Edge przekazuje żądania uwierzytelniania do usługi IoT Hub, gdy urządzenie po raz pierwszy próbuje się połączyć. Po ustanowieniu pierwszego połączenia informacje o zabezpieczeniach są buforowane lokalnie przez centrum usługi IoT Edge. Przyszłe połączenia z tego urządzenia są dozwolone bez konieczności ponownego uwierzytelniania w chmurze.

Aby zmniejszyć przepustowość używaną przez rozwiązanie Usługi IoT Edge, centrum usługi IoT Edge optymalizuje liczbę rzeczywistych połączeń z chmurą. Centrum usługi IoT Edge pobiera połączenia logiczne z modułów lub urządzeń podrzędnych i łączy je dla pojedynczego fizycznego połączenia z chmurą. Szczegóły tego procesu są przejrzyste dla pozostałej części rozwiązania. Klienci myślą, że mają własne połączenie z chmurą, nawet jeśli wszystkie są wysyłane za tym samym połączeniem.

![Usługa IoT Edge hub to brama między urządzeniami fizycznymi a centrum IoT Hub](./media/iot-edge-runtime/Gateway.png)

Centrum usługi IoT Edge może określić, czy jest on połączony z centrum IoT Hub. Jeśli połączenie zostanie utracone, centrum Usługi IoT Edge zapisuje wiadomości lub bliźniacze aktualizacje lokalnie. Po ponownym nawiązyniu połączenia synchronizuje wszystkie dane. Lokalizacja używana dla tej tymczasowej pamięci podręcznej jest określana przez właściwość bliźniaczej reprezentacji modułu usługi IoT Edge. Rozmiar pamięci podręcznej nie jest ograniczona i będzie rosnąć tak długo, jak urządzenie ma pojemność magazynu.Aby uzyskać więcej informacji, zobacz [Możliwości trybu offline](offline-capabilities.md).

### <a name="module-communication"></a>Komunikacja modułów

Piasta IoT Edge ułatwia komunikację modułów z modułami. Korzystanie z usługi IoT Edge hub jako broker wiadomości utrzymuje moduły niezależne od siebie. Moduły muszą tylko określić dane wejściowe, na których akceptują komunikaty i dane wyjściowe, do których piszą wiadomości. Deweloper rozwiązania można zszyć te dane wejściowe i wyjściowe razem, tak aby moduły przetwarzają dane w kolejności specyficznej dla tego rozwiązania.

![IoT Edge Hub ułatwia komunikację między modułami](./media/iot-edge-runtime/module-endpoints.png)

Aby wysłać dane do centrum usługi IoT Edge, moduł wywołuje metodę SendEventAsync. Pierwszy argument określa, na których danych wyjściowych wysłać wiadomość. Następujący pseudokod wysyła komunikat na **output1:**

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings);
   await client.OpenAsync();
   await client.SendEventAsync("output1", message);
   ```

Aby otrzymać wiadomość, zarejestruj wywołanie zwrotne, które przetwarza wiadomości przychodzące na określone dane wejściowe. Następujący pseudokod rejestruje komunikat funkcjiProcessor do przetwarzania wszystkich wiadomości odebranych na **input1:**

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

Aby uzyskać więcej informacji na temat klasy ModuleClient i jej metod komunikacji, zobacz odwołanie do interfejsu API preferowanego języka SDK: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)lub [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

Deweloper rozwiązania jest odpowiedzialny za określenie reguł, które określają, jak centrum usługi IoT Edge przekazuje wiadomości między modułami. Reguły routingu są definiowane w chmurze i wypychane do centrum usługi IoT Edge w bliźniaczej reprezentacji modułu. Ta sama składnia tras usługi IoT Hub służy do definiowania tras między modułami w usłudze Azure IoT Edge. Aby uzyskać więcej informacji, zobacz [Dowiedz się, jak wdrażać moduły i ustalać trasy w uliczce IoT.](module-composition.md)

![Trasy między modułami przechodzą przez centrum IoT Edge](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>Agent IoT Edge

Agent usługi IoT Edge to inny moduł, który tworzy środowisko uruchomieniowe usługi Azure IoT Edge. Jest odpowiedzialny za tworzenie wystąpienia modułów, zapewnienie, że nadal działają i raportowania stanu modułów z powrotem do Usługi IoT Hub. Te dane konfiguracji są zapisywane jako właściwość bliźniaczej reprezentacji modułu agenta usługi IoT Edge.

Demon [zabezpieczeń usługi IoT Edge](iot-edge-security-manager.md) uruchamia agenta usługi IoT Edge podczas uruchamiania urządzenia. Agent pobiera bliźniaczej reprezentacji modułu z usługi IoT Hub i sprawdza manifest wdrożenia. Manifest wdrażania jest plikiem JSON, który deklaruje moduły, które należy uruchomić.

Każdy element w manifeście wdrożenia zawiera określone informacje o module i jest używany przez agenta usługi IoT Edge do kontrolowania cyklu życia modułu. Niektóre z bardziej interesujących właściwości są:

* **settings.image** — obraz kontenera używany przez agenta usługi IoT Edge do uruchomienia modułu. Agent usługi IoT Edge musi być skonfigurowany z poświadczeniami dla rejestru kontenerów, jeśli obraz jest chroniony hasłem. Poświadczenia rejestru kontenerów można skonfigurować zdalnie przy użyciu manifestu wdrażania lub na samym `config.yaml` urządzeniu usługi IoT Edge, aktualizując plik w folderze programu usługi IoT Edge.
* **settings.createOptions** — ciąg, który jest przekazywany bezpośrednio do demona kontenera Moby podczas uruchamiania kontenera modułu. Dodawanie opcji w tej właściwości umożliwia zaawansowane konfiguracje, takie jak przekierowanie portów lub woluminy montażowe do kontenera modułu.  
* **stan** — stan, w którym agent IoT Edge umieszcza moduł. Zazwyczaj ta wartość jest ustawiona na *uruchamianie,* ponieważ większość osób chce, aby agent usługi IoT Edge natychmiast uruchamiał wszystkie moduły na urządzeniu. Można jednak określić stan początkowy modułu, który ma zostać zatrzymany i poczekać na przyszły czas, aby poinformować agenta ioT Edge o uruchomieniu modułu.Agent usługi IoT Edge raportuje stan każdego modułu z powrotem do chmury w zgłoszonych właściwościach. Różnica między żądaną właściwością a zgłoszoną właściwością jest wskaźnikiem niewłaściwego zachowania urządzenia. Obsługiwane stany to:

  * Pobieranie
  * Działanie
  * W niedobrej kondycji
  * Niepowodzenie
  * Zatrzymano

* **restartPolicy** — jak agent IoT Edge uruchamia ponownie moduł. Możliwe wartości to:
  
  * `never`– Agent IoT Edge nigdy nie uruchamia ponownie modułu.
  * `on-failure`- Jeśli moduł ulegnie awarii, agent IoT Edge uruchomi go ponownie. Jeśli moduł zostanie zamknięty czysto, agent IoT Edge nie uruchomi go ponownie.
  * `on-unhealthy`- Jeśli moduł ulega awarii lub jest uważany za w złej kondycji, agent IoT Edge uruchamia go ponownie.
  * `always`- Jeśli moduł ulegnie awarii, jest uważany za w złej kondycji lub zamyka się w jakikolwiek sposób, agent IoT Edge uruchamia go ponownie.

* **imagePullPolicy** — czy agent IoT Edge próbuje wyciągnąć najnowszy obraz dla modułu automatycznie, czy nie. Jeśli nie określisz wartości, wartość domyślna *toTworzenie*. Możliwe wartości to:

  * `on-create`- Podczas uruchamiania modułu lub aktualizowania modułu na podstawie nowego manifestu wdrażania agent usługi IoT Edge podejmie próbę wyciągnięcia obrazu modułu z rejestru kontenerów.
  * `never`- Agent usługi IoT Edge nigdy nie podejmie próby wyciągnięcia obrazu modułu z rejestru kontenerów. W tej konfiguracji użytkownik jest odpowiedzialny za uzyskanie obrazu modułu na urządzeniu i zarządzanie aktualizacjami obrazów.

Agent usługi IoT Edge wysyła odpowiedź środowiska uruchomieniowego do centrum IoT Hub. Oto lista możliwych odpowiedzi:
  
* 200 - OK
* 400 - Konfiguracja wdrożenia jest nieprawidłowo sformułowana lub nieprawidłowa.
* 417 - Urządzenie nie ma zestawu konfiguracji wdrożenia.
* 412 - Wersja schematu w konfiguracji wdrożenia jest nieprawidłowa.
* 406 - Urządzenie IoT Edge jest w trybie offline lub nie wysyła raportów o stanie.
* 500 — wystąpił błąd w czasie wykonywania IoT Edge.

Aby uzyskać więcej informacji, zobacz [Dowiedz się, jak wdrażać moduły i ustalać trasy w uliczce IoT.](module-composition.md)

### <a name="security"></a>Zabezpieczenia

Agent usługi IoT Edge odgrywa kluczową rolę w bezpieczeństwie urządzenia usługi IoT Edge. Na przykład wykonuje akcje, takie jak sprawdzanie obrazu modułu przed jego uruchomieniem.

Aby uzyskać więcej informacji na temat struktury zabezpieczeń usługi Azure IoT Edge, przeczytaj o [menedżerze zabezpieczeń usługi IoT Edge](iot-edge-security-manager.md).

## <a name="next-steps"></a>Następne kroki

[Omówienie modułów usługi Azure IoT Edge](iot-edge-modules.md)
