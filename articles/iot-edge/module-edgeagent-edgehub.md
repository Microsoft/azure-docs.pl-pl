---
title: Właściwości modułu agenta i centrum bliźniaczych reprezentacji — Azure IoT Edge
description: Przejrzyj określone właściwości i ich wartości dla modułu edgeAgent i edgeHub bliźniaczych reprezentacji
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f2d6603c264c9da3f2700f460a8c61b24681fac6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80546189"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>Właściwości agenta IoT Edge i modułu IoT Edge Hub bliźniaczych reprezentacji

Agent IoT Edge i centrum IoT Edge to dwa moduły, które tworzą środowisko uruchomieniowe IoT Edge. Aby uzyskać więcej informacji na temat obowiązków każdego modułu uruchomieniowego, zobacz [Opis środowiska uruchomieniowego Azure IoT Edge i jego architektury](iot-edge-runtime.md).

Ten artykuł zawiera odpowiednie właściwości i raportowane właściwości modułu uruchomieniowego bliźniaczych reprezentacji. Aby uzyskać więcej informacji na temat sposobu wdrażania modułów na IoT Edge urządzeniach, zobacz [informacje na temat wdrażania modułów i ustanawiania tras w programie IoT Edge](module-composition.md).

Sznurki modułu obejmuje:

* **Żądane właściwości**. Zaplecze rozwiązania może ustawić żądane właściwości, a moduł może je odczytać. Moduł może również odbierać powiadomienia o zmianach w odpowiednich właściwościach. Odpowiednie właściwości są używane wraz z zgłoszonymi właściwościami do synchronizacji konfiguracji lub warunków modułu.

* **Raportowane właściwości**. Moduł może ustawiać raportowane właściwości, a zaplecze rozwiązania może odczytywać i wysyłać do nich zapytania. Raportowane właściwości są używane wraz z pożądanymi właściwościami do synchronizacji konfiguracji lub warunków modułu.

## <a name="edgeagent-desired-properties"></a>EdgeAgent żądane właściwości

Sznurek modułu dla agenta IoT Edge jest wywoływany `$edgeAgent` i koordynuje komunikację między agentem IoT Edge działającym na urządzeniu i IoT Hub. Żądane właściwości są ustawiane podczas stosowania manifestu wdrożenia na określonym urządzeniu w ramach wdrożenia pojedynczego lub w skali.

| Właściwość | Opis | Wymagane |
| -------- | ----------- | -------- |
| schemaVersion | Musi mieć wartość "1,0" | Tak |
| Typ środowiska uruchomieniowego. | Musi być "Docker" | Tak |
| Runtime. Settings. minDockerVersion | Ustaw minimalną wersję platformy Docker wymaganą przez ten manifest wdrożenia | Tak |
| Runtime. Settings. loggingOptions | Skonwertowanej JSON zawierający opcje rejestrowania dla kontenera agenta IoT Edge. [Opcje rejestrowania platformy Docker](https://docs.docker.com/engine/admin/logging/overview/) | Nie |
| Runtime. Settings. registryCredentials<br>. {registryId}. nazwa użytkownika | Nazwa użytkownika rejestru kontenerów. W przypadku Azure Container Registry nazwa użytkownika jest zwykle nazwą rejestru.<br><br> W przypadku wszystkich obrazów modułów prywatnych wymagane są poświadczenia rejestru. | Nie |
| Runtime. Settings. registryCredentials<br>. {registryId}. hasło | Hasło dla rejestru kontenerów. | Nie |
| Runtime. Settings. registryCredentials<br>. {registryId}. Address | Adres rejestru kontenerów. W przypadku Azure Container Registry adres jest zwykle *{Nazwa rejestru}. azurecr. IO*. | Nie |  
| systemModules. edgeAgent. Type | Musi być "Docker" | Tak |
| systemModules. edgeAgent. Settings. Image | Identyfikator URI obrazu agenta IoT Edge. Obecnie Agent IoT Edge nie może zaktualizować siebie. | Tak |
| systemModules. edgeAgent. Settings<br>... | Skonwertowanej JSON zawierający opcje tworzenia kontenera agenta IoT Edge. [Opcje tworzenia platformy Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nie |
| systemModules.edgeAgent.configuration.id | Identyfikator wdrożenia, który został wdrożony w tym module. | IoT Hub ustawia tę właściwość, gdy manifest zostanie zastosowany przy użyciu wdrożenia. Nie jest częścią manifestu wdrożenia. |
| systemModules. edgeHub. Type | Musi być "Docker" | Tak |
| systemModules. edgeHub. status | Musi być "uruchomiona" | Tak |
| systemModules. edgeHub. restartPolicy | Musi mieć wartość "always" | Tak |
| systemModules. edgeHub. Settings. Image | Identyfikator URI obrazu Centrum IoT Edge. | Tak |
| systemModules. edgeHub. Settings<br>... | Skonwertowanej JSON zawierający opcje tworzenia kontenera Centrum IoT Edge. [Opcje tworzenia platformy Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nie |
| systemModules.edgeHub.configuration.id | Identyfikator wdrożenia, który został wdrożony w tym module. | IoT Hub ustawia tę właściwość, gdy manifest zostanie zastosowany przy użyciu wdrożenia. Nie jest częścią manifestu wdrożenia. |
| Moduły. {moduleId}. wersja | Zdefiniowany przez użytkownika ciąg reprezentujący wersję tego modułu. | Tak |
| Moduły. {moduleId}. typ | Musi być "Docker" | Tak |
| Moduły. {moduleId}. status | {"działa" \| "zatrzymane"} | Tak |
| Moduły. {moduleId}. restartPolicy | {"nigdy" \| "on-Failure" \| "w złej kondycji" \| "always"} | Tak |
| Moduły. {moduleId}. imagePullPolicy | {"on-Create" \| "nigdy"} | Nie |
| Moduły. {moduleId}. env | Lista zmiennych środowiskowych do przekazania do modułu. Formatuje `"<name>": {"value": "<value>"}` | Nie |
| Moduły. {moduleId}. Settings. Image | Identyfikator URI obrazu modułu. | Tak |
| Moduły. {moduleId}. Settings. SetOptions | Skonwertowanej JSON zawierający opcje tworzenia kontenera modułów. [Opcje tworzenia platformy Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nie |
| Moduły. {moduleId}. Configuration. ID | Identyfikator wdrożenia, który został wdrożony w tym module. | IoT Hub ustawia tę właściwość, gdy manifest zostanie zastosowany przy użyciu wdrożenia. Nie jest częścią manifestu wdrożenia. |

## <a name="edgeagent-reported-properties"></a>EdgeAgent raportowane właściwości

Agenci IoT Edge zgłosili właściwości zawierają trzy główne informacje:

1. Stan aplikacji ostatnio widzianych właściwości;
2. Stan modułów aktualnie uruchomionych na urządzeniu, zgłoszony przez agenta IoT Edge; lub
3. Kopia żądanych właściwości, które są aktualnie uruchomione na urządzeniu.

Kopia bieżących żądanych właściwości jest przydatna do stwierdzenia, czy urządzenie zastosowało najnowsze wdrożenie, czy nadal działa z poprzednim manifestem wdrożenia.

> [!NOTE]
> Raportowane właściwości agenta IoT Edge są przydatne, ponieważ można z nich [wysyłać zapytania przy użyciu języka zapytań IoT Hub](../iot-hub/iot-hub-devguide-query-language.md) w celu zbadania stanu wdrożeń w odpowiedniej skali. Aby uzyskać więcej informacji na temat używania IoT Edge właściwości agenta dla stanu, zobacz [opis IoT Edge wdrożeń dla pojedynczych urządzeń lub w odpowiedniej skali](module-deployment-monitoring.md).

Poniższa tabela nie zawiera informacji skopiowanych z odpowiednich właściwości.

| Właściwość | Opis |
| -------- | ----------- |
| lastDesiredVersion | Ta liczba całkowita odnosi się do ostatniej wersji żądanych właściwości przetworzonych przez agenta IoT Edge. |
| lastDesiredStatus. Code | Ten kod stanu odnosi się do ostatnich wymaganych właściwości widzianych przez agenta IoT Edge. Dozwolone wartości: `200` sukces, `400` nieprawidłowa konfiguracja, `412` nieprawidłowa wersja schematu, `417` żądane właściwości są puste, `500` Niepowodzenie |
| lastDesiredStatus. Description | Opis tekstowy stanu |
| deviceHealth | `healthy` Jeśli stan środowiska uruchomieniowego wszystkich modułów to `running` lub `stopped` , `unhealthy` w przeciwnym razie |
| configurationHealth. {deploymentId}. Health | `healthy` Jeśli stan środowiska uruchomieniowego wszystkich modułów ustawionych przez wdrożenie {deploymentId} to albo `running` `stopped` , `unhealthy` w przeciwnym razie |
| środowisko uruchomieniowe. platform. OS | Raportowanie systemu operacyjnego działającego na urządzeniu |
| środowisko uruchomieniowe. platform. Architecture | Raportowanie architektury procesora CPU na urządzeniu |
| systemModules.edgeAgent.runtimeStatus | Zgłoszony stan IoT Edge agenta: {"uruchomiona" "w \| złej kondycji"} |
| systemModules.edgeAgent.statusDescription | Opis tekstowy stanu zgłoszonego przez agenta IoT Edge. |
| systemModules.edgeHub.runtimeStatus | Stan Centrum IoT Edge: {"uruchomiono" \| "Zatrzymano" " \| nie powiodło się" " \| wycofywania" \| "w złej kondycji"} |
| systemModules.edgeHub.statusDescription | Opis tekstowy stanu Centrum IoT Edge w przypadku złej kondycji. |
| systemModules. edgeHub. exitCode | Kod zakończenia raportowany przez kontener Centrum IoT Edge w przypadku zakończenia kontenera |
| systemModules.edgeHub.startTimeUtc | Godzina ostatniego uruchomienia Centrum IoT Edge |
| systemModules.edgeHub.lastExitTimeUtc | Godzina, o której ostatnio zakończył się IoT Edge centrum |
| systemModules.edgeHub.lastRestartTimeUtc | Czas ostatniego ponownego uruchomienia Centrum IoT Edge |
| systemModules.edgeHub.restartCount | Liczba ponownych uruchomień tego modułu w ramach zasad ponownego uruchamiania. |
| Moduły. {moduleId}. runtimeStatus | Stan modułu: {"uruchomiona" \| "zatrzymała \| się" "nie powiodła się" " \| wycofywania" \| "w złej kondycji"} |
| Moduły. {moduleId}. statusDescription | Opis tekstowy stanu modułu w złej kondycji. |
| Moduły. {moduleId}. exitCode | Kod zakończenia raportowany przez kontener modułów w przypadku opuszczenia kontenera |
| Moduły. {moduleId}. startTimeUtc | Czas ostatniego uruchomienia modułu |
| Moduły. {moduleId}. lastExitTimeUtc | Czas ostatniego zakończenia modułu |
| Moduły. {moduleId}. lastRestartTimeUtc | Czas ostatniego ponownego uruchomienia modułu |
| Moduły. {moduleId}. restartCount | Liczba ponownych uruchomień tego modułu w ramach zasad ponownego uruchamiania. |

## <a name="edgehub-desired-properties"></a>EdgeHub żądane właściwości

Sznurek modułu dla Centrum IoT Edge jest wywoływany `$edgeHub` i koordynuje komunikację między centrum IoT Edge działającym na urządzeniu i IoT Hub. Żądane właściwości są ustawiane podczas stosowania manifestu wdrożenia na określonym urządzeniu w ramach wdrożenia pojedynczego lub w skali.

| Właściwość | Opis | Wymagane w manifeście wdrożenia |
| -------- | ----------- | -------- |
| schemaVersion | Musi mieć wartość "1,0" | Tak |
| rozsyłan. RouteName | Ciąg reprezentujący trasę Centrum IoT Edge. Aby uzyskać więcej informacji, zobacz [deklarowanie tras](module-composition.md#declare-routes). | `routes`Element może być obecny, ale pusty. |
| storeAndForwardConfiguration.timeToLiveSecs | Czas (w sekundach), przez jaki usługa IoT Edge Hub zachowuje komunikaty, jeśli są odłączone od punktów końcowych routingu, niezależnie od tego, czy IoT Hub czy moduł lokalny Wartość może być dowolną dodatnią liczbą całkowitą. | Tak |

## <a name="edgehub-reported-properties"></a>EdgeHub raportowane właściwości

| Właściwość | Opis |
| -------- | ----------- |
| lastDesiredVersion | Ta liczba całkowita odnosi się do ostatniej wersji żądanych właściwości przetworzonych przez Centrum IoT Edge. |
| lastDesiredStatus. Code | Kod stanu odnoszący się do ostatnich żądanych właściwości widzianych przez Centrum IoT Edge. Dozwolone wartości: `200` sukces, `400` nieprawidłowa konfiguracja, `500` Niepowodzenie |
| lastDesiredStatus. Description | Opis tekstowy stanu. |
| klientem. {Device lub moduleId}. status | Stan łączności tego urządzenia lub modułu. Możliwe wartości {"Connected" \| "disconnected"}. Tylko tożsamości modułów mogą być w stanie odłączonym. Urządzenia podrzędne łączące się z Centrum IoT Edge są wyświetlane tylko po nawiązaniu połączenia. |
| klientem. {Device lub moduleId}. lastConnectTime | Czas ostatniego połączenia urządzenia lub modułu. |
| klientem. {Device lub moduleId}. lastDisconnectTime | Czas ostatniego odłączenia urządzenia lub modułu. |

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak używać tych właściwości do kompilowania manifestów wdrożenia, zobacz [Opis sposobu używania, konfigurowania i ponownego użycia modułów IoT Edge](module-composition.md).
