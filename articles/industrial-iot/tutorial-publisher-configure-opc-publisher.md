---
title: Konfigurowanie wydawcy programu Microsoft OPCe
description: W tym samouczku dowiesz się, jak skonfigurować wydawcę OPC w trybie autonomicznym.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 4d4f9c90fd96365216480164f29f08fad92eb9d0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787808"
---
# <a name="tutorial-configure-the-opc-publisher"></a>Samouczek: Konfigurowanie wydawcy OPC

W tym samouczku znajdują się informacje dotyczące konfiguracji wydawcy OPC. Do konfiguracji można użyć kilku interfejsów.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie wydawcy OPC za pośrednictwem pliku konfiguracji
> * Konfigurowanie wydawcy OPC za pośrednictwem argumentów wiersza polecenia
> * Konfigurowanie wydawcy OPC przy użyciu metod bezpośrednich IoT Hub
> * Konfigurowanie wydawcy OPC za pośrednictwem usługi Cloud-based Companion REST

## <a name="configuring-security"></a>Konfigurowanie zabezpieczeń

IoT Edge zapewnia wydawcy OPC z konfiguracją zabezpieczeń w celu automatycznego uzyskiwania dostępu do IoT Hub. Wydawca OPC może również działać jako autonomiczny kontener platformy Docker przez określenie parametrów połączenia urządzenia do uzyskiwania dostępu do IoT Hub za pośrednictwem `dc` parametru wiersza polecenia. Można utworzyć urządzenie dla IoT Hub i jego parametry połączenia pobierane za pomocą Azure Portal.

W celu uzyskania dostępu do zasobów z obsługą OPC UA, certyfikatów X. 509 i skojarzonych z nimi kluczy prywatnych są używane przez OPC UA. Jest to nazywane uwierzytelnianiem aplikacji OPC UA oraz uwierzytelnianiem użytkownika OPC UA. Program OPC Publisher używa magazynu certyfikatów opartego na systemie plików do zarządzania wszystkimi certyfikatami aplikacji. Podczas uruchamiania, OPC Wydawca sprawdza, czy istnieje certyfikat, którego można użyć w tych magazynach certyfikatów i tworzy nowy certyfikat z podpisem własnym i nowy skojarzony klucz prywatny, jeśli nie istnieje. Certyfikaty z podpisem własnym zapewniają słabą autoryzację, ponieważ nie są podpisane przez zaufany urząd certyfikacji, ale co najmniej komunikacja z zasobem z włączonym OPC UA może być szyfrowana w ten sposób.

Zabezpieczenia są włączone w pliku konfiguracji przez `"UseSecurity": true,` flagę. Najbardziej bezpiecznym punktem końcowym dostępnym na serwerach OPC UA, z którym ma zostać nawiązane połączenie, jest wybrany automatycznie.
Domyślnie Wydawca programu OPC używa uwierzytelniania użytkownika anonimowego (dodatkowo w przypadku uwierzytelniania aplikacji opisanego powyżej). Jednak program OPC Publisher obsługuje także uwierzytelnianie użytkowników przy użyciu nazwy użytkownika i hasła. Można to określić za pomocą interfejsu konfiguracji interfejsu API REST (opisanego poniżej) lub pliku konfiguracji w następujący sposób:
```
"OpcAuthenticationMode": "UsernamePassword",
"OpcAuthenticationUsername": "usr",
"OpcAuthenticationPassword": "pwd",
```
Ponadto Wydawca OPC w wersji 2,5 i poniżej szyfruje nazwę użytkownika i hasło w pliku konfiguracji. Wersja 2,6 i nowsze obsługuje tylko nazwę użytkownika i hasło w postaci zwykłego tekstu. Zostanie to udoskonalone w następnej wersji wydawcy programu OPC.

Aby zachować konfigurację zabezpieczeń wydawcy programu OPC w ramach ponownych uruchomień, certyfikat i klucz prywatny znajdujący się w katalogu magazynu certyfikatów musi być zamapowany na IoT Edge system plików systemu operacyjnego hosta. Zobacz sekcję "Określanie opcji tworzenia kontenera w Azure Portal" powyżej.

## <a name="configuration-via-configuration-file"></a>Konfiguracja za pośrednictwem pliku konfiguracji

Najprostszym sposobem skonfigurowania wydawcy OPC jest za pośrednictwem pliku konfiguracji. Przykładowy plik konfiguracji oraz dokumentacja dotycząca jego formatu są udostępniane za pośrednictwem pliku [`publishednodes.json`](https://raw.githubusercontent.com/Azure/iot-edge-opc-publisher/master/opcpublisher/publishednodes.json) w tym repozytorium.
Składnia pliku konfiguracji została zmieniona z upływem czasu, a wydawca OPC nadal może odczytywać stare formaty, ale konwertuje je do najnowszego formatu w przypadku utrwalania konfiguracji, regularnie w sposób zautomatyzowany.

Plik konfiguracji podstawowej wygląda następująco:
```
[
  {
    "EndpointUrl": "opc.tcp://testserver:62541/Quickstarts/ReferenceServer",
    "UseSecurity": true,
    "OpcNodes": [
      {
        "Id": "i=2258",
        "OpcSamplingInterval": 2000,
        "OpcPublishingInterval": 5000,
        "DisplayName": "Current time"
      }
    ]
  }
]
```

Zasoby OPC UA optymalizują przepustowość sieci przez wysyłanie zmian danych do wydawcy OPC w przypadku zmiany danych. Jeśli zmiany danych muszą być publikowane częściej lub w regularnych odstępach czasu, OPC Publisher obsługuje "Puls" dla każdego skonfigurowanego elementu danych, który można włączyć, dodatkowo określając klucz HeartbeatInterval w konfiguracji elementu danych. Interwał jest określony w sekundach:
```
 "HeartbeatInterval": 3600,
```

Zasób OPC UA zawsze wysyła bieżącą wartość elementu danych, gdy program OPC Publisher po raz pierwszy łączy się z nim. Aby zapobiec opublikowaniu tych danych w IoT Hub, można dodatkowo określić klucz SkipFirst w konfiguracji elementu danych:
```
 "SkipFirst": true,
```

Oba ustawienia można również włączyć globalnie za pomocą opcji wiersza polecenia.

## <a name="configuration-via-command-line-arguments"></a>Konfiguracja za pośrednictwem argumentów wiersza polecenia

Istnieje kilka argumentów wiersza polecenia, których można użyć do ustawiania ustawień globalnych dla wydawcy OPC. Opisano je [tutaj](reference-command-line-arguments.md).


## <a name="configuration-via-the-built-in-opc-ua-server-interface"></a>Konfiguracja za pośrednictwem wbudowanego interfejsu serwera OPC UA

>[!NOTE] 
> Ta funkcja jest dostępna tylko w wersji 2,5 i poniżej OPC wydawcy. * *

Wydawca OPC ma wbudowany serwer OPC UA uruchomiony na porcie 62222. Implementuje trzy metody OPC UA:

  - PublishNode
  - UnpublishNode
  - GetPublishedNodes

Dostęp do tego interfejsu można uzyskać za pomocą aplikacji klienckiej OPC UA, na przykład [eksperta UA](https://www.unified-automation.com/products/development-tools/uaexpert.html).

## <a name="configuration-via-iot-hub-direct-methods"></a>Konfiguracja za pomocą metod bezpośrednich IoT Hub

>[!NOTE] 
> Ta funkcja jest dostępna tylko w wersji 2,5 i poniżej OPC wydawcy. * *

Wydawca OPC implementuje IoT Hub następujące [metody bezpośrednie](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-direct-methods), które mogą być wywoływane z aplikacji (z dowolnego miejsca na świecie) przy użyciu [zestawu SDK IoT Hub urządzenia](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks):

  - PublishNodes
  - UnpublishNodes
  - UnpublishAllNodes
  - GetConfiguredEndpoints
  - GetConfiguredNodesOnEndpoint
  - GetDiagnosticInfo
  - GetDiagnosticLog
  - GetDiagnosticStartupLog
  - ExitApplication
  - GetInfo

Firma Microsoft udostępniła [przykładową aplikację konfiguracyjną](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration) oraz [aplikację do odczytywania informacji diagnostycznych](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics) ze źródła OPC wydawcy Open Source, wykorzystując ten interfejs.

## <a name="configuration-via-cloud-based-companion-rest-microservice"></a>Konfiguracja za pośrednictwem chmurowej, pomocniczej mikrousługi REST

>[!NOTE] 
> Ta funkcja jest dostępna tylko w wersji 2,6 i nowszej OPC wydawcy.

W [tym miejscu](https://github.com/Azure/Industrial-IoT/blob/master/docs/services/publisher.md)opisano i udostępniono mikrousług w chmurze, które współdziałają z interfejsem Rest. Można go użyć do skonfigurowania wydawcy OPC za pośrednictwem interfejsu zgodnego z OpenAPI, na przykład za pośrednictwem struktury Swagger.

## <a name="configuration-of-the-simple-json-telemetry-format-via-separate-configuration-file"></a>Konfiguracja prostego formatu danych telemetrycznych JSON za pośrednictwem oddzielnego pliku konfiguracji

>[!NOTE] 
> Ta funkcja jest dostępna tylko w wersji 2,5 i poniżej wydawcy programu OPC.

Wydawca OPC umożliwia filtrowanie części niestandardowego, prostego formatu telemetrii za pośrednictwem oddzielnego pliku konfiguracji, który można określić za pomocą opcji wiersza polecenia TC. Jeśli plik konfiguracji nie zostanie określony, pełny format danych telemetrycznych JSON zostanie wysłany do IoT Hub. W [tym miejscu](reference-opc-publisher-telemetry-format.md#opc-publisher-telemetry-configuration-file-format)opisano format oddzielnego pliku konfiguracji telemetrii.

## <a name="next-steps"></a>Następne kroki
Teraz, po skonfigurowaniu wydawcy OPC, następnym krokiem jest zapoznanie się z tematem jak dostosować wydajność i pamięć modułu brzegowego:

> [!div class="nextstepaction"]
> [Dostrajanie wydajności i pamięci](tutorial-publisher-performance-memory-tuning-opc-publisher.md)