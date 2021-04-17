---
title: Właściwości bliźniaczych reprezentacji agenta i modułu centrum — Azure IoT Edge
description: Przejrzyj konkretne właściwości i ich wartości dla bliźniaczych reprezentacji modułu edgeAgent i edgeHub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/16/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 29ec958764f4a464d51f29f4b9c8223d5d7a1760
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576010"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>Właściwości agenta IoT Edge i IoT Edge bliźniaczych reprezentacji modułu centrum danych

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Agent IoT Edge i centrum IoT Edge to dwa moduły, które IoT Edge uruchomieniowe. Aby uzyskać więcej informacji na temat obowiązków poszczególnych modułów środowiska uruchomieniowego, zobacz Understand the Azure IoT Edge runtime and its architecture (Informacje o środowisku uruchomieniowym Azure IoT Edge [i jego architekturze).](iot-edge-runtime.md)

Ten artykuł zawiera żądane właściwości i zgłaszane właściwości bliźniaczych reprezentacji modułu środowiska uruchomieniowego. Aby uzyskać więcej informacji na temat wdrażania modułów na urządzeniach IoT Edge, zobacz Dowiedz się, jak wdrażać moduły i [ustanawiać trasy](module-composition.md)w IoT Edge .

Bliźniacze reprezentacji modułu:

* **Żądane właściwości**. Za pomocą zaplecza rozwiązania można ustawić żądane właściwości, a moduł może je odczytać. Moduł może również otrzymywać powiadomienia o zmianach w żądanych właściwościach. Żądane właściwości są używane wraz ze zgłaszanych właściwościami do synchronizowania konfiguracji lub warunków modułu.

* **Zgłaszane właściwości**. Moduł może ustawiać zgłaszane właściwości, a zaplecza rozwiązania mogą je odczytywać i odpytują. Zgłaszane właściwości są używane wraz z żądanymi właściwościami do synchronizowania konfiguracji lub warunków modułu.

## <a name="edgeagent-desired-properties"></a>Żądane właściwości EdgeAgent

Bliźniacze reprezentacji modułu IoT Edge i koordynuje komunikację między agentem IoT Edge uruchomionym na urządzeniu `$edgeAgent` i IoT Hub. Żądane właściwości są ustawiane podczas stosowania manifestu wdrożenia na określonym urządzeniu w ramach wdrożenia na jednym urządzeniu lub w skali.

| Właściwość | Opis | Wymagane |
| -------- | ----------- | -------- |
| schemaVersion | Albo "1.0" lub "1.1". Wersja 1.1 została wprowadzona w IoT Edge wersji 1.0.10 i jest zalecana. | Yes |
| runtime.type | Musi to być "docker" | Yes |
| runtime.settings.minDockerVersion | Ustaw minimalną wersję platformy Docker wymaganą przez ten manifest wdrożenia | Yes |
| runtime.settings.loggingOptions | Ciągowany plik JSON zawierający opcje rejestrowania dla IoT Edge agenta. [Opcje rejestrowania platformy Docker](https://docs.docker.com/engine/admin/logging/overview/) | Nie |
| runtime.settings.registryCredentials<br>. {registryId}.username | Nazwa użytkownika rejestru kontenerów. W Azure Container Registry nazwa użytkownika jest zazwyczaj nazwą rejestru.<br><br> Poświadczenia rejestru są niezbędne dla wszystkich prywatnych obrazów modułów. | Nie |
| runtime.settings.registryCredentials<br>. {registryId}.password | Hasło rejestru kontenerów. | Nie |
| runtime.settings.registryCredentials<br>. {registryId}.address | Adres rejestru kontenerów. W Azure Container Registry adres to zwykle *{nazwa rejestru}.azurecr.io*. | Nie |  
| systemModules.edgeAgent.type | Musi to być "docker" | Yes |
| systemModules.edgeAgent.settings.image | URI obrazu agenta IoT Edge. Obecnie agent IoT Edge nie może się zaktualizować. | Yes |
| systemModules.edgeAgent.settings<br>.createOptions | Ciągowany plik JSON zawierający opcje tworzenia kontenera IoT Edge agenta. [Opcje tworzenia platformy Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nie |
| systemModules.edgeAgent.configuration.id | Identyfikator wdrożenia, które wdrożyło ten moduł. | IoT Hub ustawia tę właściwość podczas stosowania manifestu przy użyciu wdrożenia. Nie jest częścią manifestu wdrożenia. |
| systemModules.edgeHub.type | Musi to być "docker" | Yes |
| systemModules.edgeHub.status | Musi być "uruchomiona" | Yes |
| systemModules.edgeHub.restartPolicy | Musi być "zawsze" | Yes |
| systemModules.edgeHub.startupOrder | Wartość całkowita, dla której moduł znajduje się w kolejności uruchamiania. 0 to pierwsza, a maksymalna liczba całkowita (4294967295) jest ostatnia. Jeśli wartość nie zostanie podany, wartość domyślna to maksymalna liczba całkowita.  | Nie |
| systemModules.edgeHub.settings.image | URI obrazu centrum IoT Edge danych. | Yes |
| systemModules.edgeHub.settings<br>.createOptions | Ciągi JSON zawierające opcje tworzenia kontenera IoT Edge hub. [Opcje tworzenia platformy Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nie |
| systemModules.edgeHub.configuration.id | Identyfikator wdrożenia, które wdrożyło ten moduł. | IoT Hub ustawia tę właściwość podczas stosowania manifestu przy użyciu wdrożenia. Nie jest częścią manifestu wdrożenia. |
| Moduły. {moduleId}.version | Ciąg zdefiniowany przez użytkownika reprezentujący wersję tego modułu. | Yes |
| Moduły. {moduleId}.type | Musi to być "docker" | Yes |
| Moduły. {moduleId}.status | {"running" \| "zatrzymane"} | Yes |
| Moduły. {moduleId}.restartPolicy | {"never" \| "on-failure" \| "on-unhealthy" \| "always"} | Yes |
| Moduły. {moduleId}.startupOrder | Wartość całkowita, dla której punkt modułu znajduje się w kolejności uruchamiania. Wartość 0 jest pierwszą, a maksymalna liczba całkowita (4294967295) jest ostatnią. Jeśli nie podano wartości, wartością domyślną jest maksymalna liczba całkowita.  | Nie |
| Moduły. {moduleId}.imagePullPolicy | {"on-create" \| "never"} | Nie |
| Moduły. {moduleId}.env | Lista zmiennych środowiskowych do przekazania do modułu. Przyjmuje format `"<name>": {"value": "<value>"}` | Nie |
| Moduły. {moduleId}.settings.image | URI obrazu modułu. | Yes |
| Moduły. {moduleId}.settings.createOptions | Ciągowany plik JSON zawierający opcje tworzenia kontenera modułu. [Opcje tworzenia platformy Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nie |
| Moduły. {moduleId}.configuration.id | Identyfikator wdrożenia, które wdrożyło ten moduł. | IoT Hub ustawia tę właściwość podczas stosowania manifestu przy użyciu wdrożenia. Nie jest częścią manifestu wdrożenia. |

## <a name="edgeagent-reported-properties"></a>Zgłoszone właściwości edgeAgent

Raporty IoT Edge agenta zawierają trzy główne informacje:

1. Stan zastosowania ostatnio widocznych żądanych właściwości;
2. Stan modułów aktualnie uruchomionych na urządzeniu, zgodnie z raportem IoT Edge agenta; I
3. Kopia żądanych właściwości aktualnie uruchomionych na urządzeniu.

Kopia bieżących żądanych właściwości jest przydatna do ustalenia, czy urządzenie zastosowało najnowsze wdrożenie, czy nadal uruchamia poprzedni manifest wdrożenia.

> [!NOTE]
> Zgłaszane właściwości agenta usługi IoT Edge są przydatne, ponieważ można do nich [](../iot-hub/iot-hub-devguide-query-language.md) odpytać za pomocą języka zapytań IoT Hub, aby zbadać stan wdrożeń na dużą skalę. Aby uzyskać więcej informacji na temat używania właściwości agenta usługi IoT Edge dla stanu, zobacz Understand IoT Edge deployments for single devices or at scale (Opis IoT Edge dla pojedynczych urządzeń lub [na dużą skalę).](module-deployment-monitoring.md)

W poniższej tabeli nie przedstawiono informacji skopiowanych z żądanych właściwości.

| Właściwość | Opis |
| -------- | ----------- |
| lastDesiredVersion | Ta liczba całkowita odnosi się do ostatniej wersji żądanych właściwości przetworzonych przez IoT Edge agenta. |
| lastDesiredStatus.code | Ten kod stanu odwołuje się do ostatnich żądanych właściwości widocznych IoT Edge agenta. Dozwolone wartości: `200` Powodzenie, `400` Nieprawidłowa konfiguracja, `412` Nieprawidłowa wersja schematu, `417` żądane właściwości są puste, `500` Niepowodzenie |
| lastDesiredStatus.description | Opis tekstowy stanu |
| configurationHealth. {deploymentId}.health | `healthy` Jeśli stan środowiska uruchomieniowego wszystkich modułów ustawiony przez wdrożenie {deploymentId} ma wartość `running` lub , w przeciwnym `stopped` `unhealthy` razie |
| runtime.platform.OS | Raportowanie systemu operacyjnego uruchomionego na urządzeniu |
| runtime.platform.architecture | Raportowanie architektury procesora CPU na urządzeniu |
| systemModules.edgeAgent.runtimeStatus | Zgłaszany stan agenta IoT Edge: {"running" \| "unhealthy"} |
| systemModules.edgeAgent.statusDescription | Opis tekstowy zgłoszonego stanu IoT Edge agenta. |
| systemModules.edgeHub.runtimeStatus | Stan centrum IoT Edge: { "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| systemModules.edgeHub.statusDescription | Opis tekstowy stanu centrum IoT Edge w złej kondycji. |
| systemModules.edgeHub.exitCode | Kod zakończenia zgłoszony przez kontener IoT Edge Hub, jeśli kontener zakończy działanie |
| systemModules.edgeHub.startTimeUtc | Godzina ostatniego IoT Edge centrum danych |
| systemModules.edgeHub.lastExitTimeUtc | Godzina ostatniego IoT Edge zakończenia centrum |
| systemModules.edgeHub.lastRestartTimeUtc | Godzina ostatniego IoT Edge centrum danych |
| systemModules.edgeHub.restartCount | Liczba ponownego uruchomienia tego modułu w ramach zasad ponownego uruchamiania. |
| Moduły. {moduleId}.runtimeStatus | Stan modułu: { "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| Moduły. {moduleId}.statusDescription | Opis tekstowy stanu modułu, jeśli jest w złej kondycji. |
| Moduły. {moduleId}.exitCode | Kod zakończenia zgłoszony przez kontener modułu, jeśli kontener zakończy działanie |
| Moduły. {moduleId}.startTimeUtc | Godzina ostatniego rozpoczęcia modułu |
| Moduły. {moduleId}.lastExitTimeUtc | Godzina ostatniego zakończenia modułu |
| Moduły. {moduleId}.lastRestartTimeUtc | Godzina ostatniego ponownego uruchomienia modułu |
| Moduły. {moduleId}.restartCount | Liczba ponownego uruchomienia tego modułu w ramach zasad ponownego uruchamiania. |

## <a name="edgehub-desired-properties"></a>Żądane właściwości usługi EdgeHub

Bliźniacze reprezentacji modułu IoT Edge hub jest wywoływane i koordynuje komunikację między centrum IoT Edge uruchomionym na urządzeniu `$edgeHub` i IoT Hub. Żądane właściwości są ustawiane podczas stosowania manifestu wdrożenia na określonym urządzeniu w ramach wdrożenia na jednym urządzeniu lub w skali.

| Właściwość | Opis | Wymagane w manifeście wdrożenia |
| -------- | ----------- | -------- |
| schemaVersion | Albo "1.0" lub "1.1". Wersja 1.1 została wprowadzona w IoT Edge wersji 1.0.10 i jest zalecana. | Yes |
| Trasy. {routeName} | Ciąg reprezentujący trasę IoT Edge centrum. Aby uzyskać więcej informacji, zobacz [Deklarowanie tras](module-composition.md#declare-routes). | Element `routes` może być obecny, ale pusty. |
| storeAndForwardConfiguration.timeToLiveSecs | Czas w sekundach, przez który centrum IoT Edge, przechowuje komunikaty w przypadku rozłączenia z punktami końcowymi routingu, niezależnie IoT Hub od modułu lokalnego lub modułu lokalnego. Wartość może być dowolną dodatnią liczbą całkowitą. | Yes |

## <a name="edgehub-reported-properties"></a>Zgłoszone właściwości usługi EdgeHub

| Właściwość | Opis |
| -------- | ----------- |
| lastDesiredVersion | Ta liczba całkowita odnosi się do ostatniej wersji żądanych właściwości przetworzonych przez IoT Edge centrum. |
| lastDesiredStatus.code | Kod stanu odwołujący się do ostatnich żądanych właściwości widocznych IoT Edge centrum. Dozwolone wartości: `200` Powodzenie, `400` Nieprawidłowa konfiguracja, `500` Niepowodzenie |
| lastDesiredStatus.description | Tekstowy opis stanu. |
| Klientów. {device lub moduleId}.status | Stan łączności tego urządzenia lub modułu. Możliwe wartości {"connected" \| "disconnected"}. Tylko tożsamości modułów mogą być w stanie rozłączenia. Urządzenia podrzędne łączące się z IoT Edge są wyświetlane tylko wtedy, gdy są połączone. |
| Klientów. {device lub moduleId}.lastConnectTime | Czas ostatniego połączenia urządzenia lub modułu. |
| Klientów. {device lub moduleId}.lastDisconnectTime | Czas ostatniego odłączenia urządzenia lub modułu. |

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak używać tych właściwości do tworzenia manifestów wdrażania, zobacz Understand [how IoT Edge modules can be used, configured, and reused](module-composition.md)(Opis sposobu używania, konfigurowania i ponownego używania modułów IoT Edge wdrożenia).
