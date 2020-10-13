---
title: Wdróż & module Routes z manifestami wdrożenia — Azure IoT Edge
description: Dowiedz się, w jaki sposób manifest wdrożenia deklaruje, które moduły wdrożyć, jak je wdrożyć, oraz jak tworzyć trasy komunikatów między nimi.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/08/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3f6c12b892e01aafd5beecdff14751481cf7fc96
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91963401"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Learn how to deploy modules and establish routes in IoT Edge (Dowiedz się, jak wdrażać moduły i ustanawiać trasy w usłudze IoT Edge).

Każde urządzenie IoT Edge uruchamia co najmniej dwa moduły: $edgeAgent i $edgeHub, które są częścią środowiska uruchomieniowego IoT Edge. Na urządzeniu IoT Edge można uruchamiać wiele dodatkowych modułów dla dowolnej liczby procesów. Użyj manifestu wdrożenia, aby poinformować urządzenie, które moduły instalować i jak skonfigurować je do pracy ze sobą.

*Manifest wdrożenia* jest dokumentem JSON opisującym:

* Moduł **IoT Edge Agent** , który obejmuje trzy składniki:
  * Obraz kontenera dla każdego modułu, który działa na urządzeniu.
  * Poświadczenia umożliwiające dostęp do prywatnych rejestrów kontenerów zawierających obrazy modułów.
  * Instrukcje dotyczące sposobu tworzenia i zarządzania każdym modułem.
* **IoT Edgey moduł centralny** , który obejmuje sposób przepływu komunikatów między modułami i ostatecznie do IoT Hub.
* Żądane właściwości dowolnego dodatkowego modułu bliźniaczych reprezentacji (opcjonalnie).

Wszystkie urządzenia IoT Edge muszą być skonfigurowane przy użyciu manifestu wdrożenia. Nowo zainstalowany IoT Edge środowisko uruchomieniowe raportuje kod błędu do momentu skonfigurowania prawidłowego manifestu.

W samouczkach Azure IoT Edge tworzysz manifest wdrożenia, przechodząc przez kreatora w portalu Azure IoT Edge. Można również zastosować manifest wdrożenia programowo przy użyciu interfejsu REST lub IoT Hub usługi SDK. Aby uzyskać więcej informacji, zobacz [Omówienie wdrożeń IoT Edge](module-deployment-monitoring.md).

## <a name="create-a-deployment-manifest"></a>Tworzenie manifestu wdrożenia

Na wysokim poziomie manifest wdrożenia jest listą bliźniaczych reprezentacji modułów, które są skonfigurowane przy użyciu odpowiednich właściwości. Manifest wdrożenia informuje urządzenie IoT Edge (lub grupę urządzeń), które moduły zainstalować i jak je skonfigurować. Manifesty wdrożenia obejmują *odpowiednie właściwości* dla każdego sznurka modułu. IoT Edge urządzeń raportuje z powrotem *raportowane właściwości* dla każdego modułu.

W każdym manifeście wdrożenia są wymagane dwa moduły: `$edgeAgent` , i `$edgeHub` . Te moduły są częścią środowiska uruchomieniowego IoT Edge, które zarządza urządzeniem IoT Edge i uruchomionymi na nim modułami. Aby uzyskać więcej informacji o tych modułach, zobacz [Omówienie środowiska uruchomieniowego IoT Edge i jego architektury](iot-edge-runtime.md).

Oprócz dwóch modułów środowiska uruchomieniowego można dodać do 50 modułów do uruchomienia na urządzeniu IoT Edge.

Manifest wdrożenia zawierający tylko środowisko uruchomieniowe IoT Edge (edgeAgent i edgeHub) jest prawidłowy.

Manifesty wdrożenia są zgodne z tą strukturą:

```json
{
  "modulesContent": {
    "$edgeAgent": { // required
      "properties.desired": {
        // desired properties of the IoT Edge agent
        // includes the image URIs of all deployed modules
        // includes container registry credentials
      }
    },
    "$edgeHub": { //required
      "properties.desired": {
        // desired properties of the IoT Edge hub
        // includes the routing information between modules, and to IoT Hub
      }
    },
    "module1": {  // optional
      "properties.desired": {
        // desired properties of module1
      }
    },
    "module2": {  // optional
      "properties.desired": {
        // desired properties of module2
      }
    }
  }
}
```

## <a name="configure-modules"></a>Konfiguruj moduły

Zdefiniuj, w jaki sposób środowisko uruchomieniowe IoT Edge instaluje moduły we wdrożeniu. Agent IoT Edge jest składnikiem środowiska uruchomieniowego, który zarządza instalacją, aktualizacjami i raportowaniem stanu dla urządzenia IoT Edge. Z tego względu sznury modułu $edgeAgent zawiera informacje o konfiguracji i zarządzaniu dla wszystkich modułów. Te informacje obejmują parametry konfiguracji dla samego agenta IoT Edge.

Właściwości $edgeAgent są zgodne z tą strukturą:

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "runtime": {
          "settings":{
            "registryCredentials":{
              // give the IoT Edge agent access to container images that aren't public
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            // configuration and management details
          },
          "edgeHub": {
            // configuration and management details
          }
        },
        "modules": {
          "module1": {
            // configuration and management details
          },
          "module2": {
            // configuration and management details
          }
        }
      }
    },
    "$edgeHub": { ... },
    "module1": { ... },
    "module2": { ... }
  }
}
```

Schemat agenta IoT Edge wersja 1,1 została wydana wraz z IoT Edge wersja 1.0.10 i włącza kolejność uruchamiania modułu. Wersja schematu 1,1 jest zalecana w przypadku wdrożenia IoT Edge w wersji 1.0.10 lub nowszej.

### <a name="module-configuration-and-management"></a>Konfiguracja modułu i zarządzanie nim

Lista żądanych właściwości agenta IoT Edge służy do definiowania, które moduły są wdrażane na urządzeniu IoT Edge i jak powinny być skonfigurowane i zarządzane.

Aby zapoznać się z pełną listą żądanych właściwości, które mogą lub muszą być zawarte, zobacz [Właściwości agenta IoT Edge i centrum IoT Edge](module-edgeagent-edgehub.md).

Na przykład:

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "runtime": { ... },
        "systemModules": {
          "edgeAgent": { ... },
          "edgeHub": { ... }
        },
        "modules": {
          "module1": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 2,
            "settings": {
              "image": "myacr.azurecr.io/module1:latest",
              "createOptions": "{}"
            }
          },
          "module2": { ... }
        }
      }
    },
    "$edgeHub": { ... },
    "module1": { ... },
    "module2": { ... }
  }
}
```

Każdy moduł ma właściwość **ustawień** , która zawiera **obraz**modułu, adres dla obrazu kontenera w rejestrze kontenerów oraz wszelkie **Opcje** konfigurowania obrazu podczas uruchamiania. Aby uzyskać więcej informacji, zobacz [jak skonfigurować opcje tworzenia kontenerów dla modułów IoT Edge](how-to-use-create-options.md).

Moduł edgeHub i moduły niestandardowe mają również trzy właściwości, które poinformują agenta IoT Edge, jak zarządzać nimi:

* **Stan**: czy moduł powinien być uruchomiony lub zatrzymany podczas pierwszego wdrożenia. Wymagane.
* **RestartPolicy**: Kiedy i czy Agent IoT Edge powinien ponownie uruchomić moduł w przypadku jego zatrzymania. Wymagane.
* **StartupOrder**: *wprowadzono w IoT Edge wersja 1.0.10.* Kolejność, w której Agent IoT Edge powinien uruchomić Moduły po ich pierwszym wdrożeniu. Kolejność jest zadeklarowana z liczbami całkowitymi, w których moduł o wartości początkowej 0 jest uruchamiany jako pierwszy, a następnie większe liczby. Moduł edgeAgent nie ma wartości początkowej, ponieważ zawsze rozpoczyna się w pierwszej kolejności. Opcjonalny.

  Agent IoT Edge inicjuje moduły w kolejności wartości początkowej, ale nie czeka na zakończenie działania każdego modułu przed przejściem do kolejnego.

  Kolejność uruchamiania jest przydatna, jeśli niektóre moduły zależą od innych. Na przykład można uruchomić moduł edgeHub jako pierwszy, aby był gotowy do trasy komunikatów po uruchomieniu innych modułów. Można też uruchomić moduł magazynu przed modułami, które wysyłają do niego dane. Należy jednak zawsze projektować moduły do obsługi błędów innych modułów. Jest to charakter kontenerów, które mogą zostać zatrzymane i ponownie uruchomione w dowolnym momencie i dowolną liczbę razy.

## <a name="declare-routes"></a>Deklarowanie tras

IoT Edge Hub zarządza komunikacją między modułami, IoT Hub i dowolnym urządzeniem liścia. Z tego względu sznurek modułu $edgeHub zawiera pożądaną właściwość o nazwie *trasy* , które deklarują sposób przekazywania komunikatów w ramach wdrożenia. W ramach tego samego wdrożenia można mieć wiele tras.

Trasy są deklarowane w **$edgeHub** żądanych właściwości z następującą składnią:

```json
{
  "modulesContent": {
    "$edgeAgent": { ... },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "routes": {
          "route1": "FROM <source> WHERE <condition> INTO <sink>",
          "route2": {
            "route": "FROM <source> WHERE <condition> INTO <sink>",
            "priority": 0,
            "timeToLiveSecs": 86400
          }
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 10
        }
      }
    },
    "module1": { ... },
    "module2": { ... }
  }
}
```

Wersja 1,1 schematu Centrum IoT Edge została wydana wraz z IoT Edge wersja 1.0.10 i umożliwia określenie priorytetów tras oraz czasu wygaśnięcia. Wersja schematu 1,1 jest zalecana w przypadku wdrożenia IoT Edge w wersji 1.0.10 lub nowszej.

Każda trasa wymaga *źródła* , z którego pochodzą wiadomości, i *ujścia* , do którego się odchodzą. *Warunek* to opcjonalny element, którego można użyć do filtrowania komunikatów.

*Priorytet* można przypisać do tras, które mają mieć na celu najpierw przetworzenie komunikatów. Ta funkcja jest przydatna w scenariuszach, w których połączenie nadrzędne jest słabe lub ograniczone i ma krytyczne dane, które powinny być priorytetowe za pośrednictwem standardowych komunikatów telemetrycznych.

### <a name="source"></a>Element źródłowy

Źródło określa, skąd pochodzą komunikaty. IoT Edge może kierować wiadomości z modułów lub urządzeń liścia.

Korzystając z zestawów SDK IoT, moduły mogą deklarować określone kolejki wyjściowe dla swoich komunikatów przy użyciu klasy ModuleClient. Kolejki wyjściowe nie są potrzebne, ale są pomocne w zarządzaniu wieloma trasami. Urządzenia liścia mogą używać klasy DeviceClient zestawów SDK IoT do wysyłania komunikatów do urządzeń bramy IoT Edge w taki sam sposób, że wysyłają komunikaty do IoT Hub. Aby uzyskać więcej informacji, zobacz [Omówienie i używanie zestawów sdk IoT Hub platformy Azure](../iot-hub/iot-hub-devguide-sdks.md).

Właściwość Source może mieć jedną z następujących wartości:

| Źródło | Opis |
| ------ | ----------- |
| `/*` | Wszystkie komunikaty z urządzenia do chmury lub dwuosiowe powiadomienia o zmianie z dowolnego modułu lub urządzenia typu liść |
| `/twinChangeNotifications` | Wszelkie zmiany przędzenia (raportowane właściwości) pochodzące z dowolnego modułu lub urządzenia typu liść |
| `/messages/*` | Wszystkie komunikaty z urządzenia do chmury wysyłane przez moduł za pomocą niektórych lub niewyjściowych lub przez urządzenie liścia |
| `/messages/modules/*` | Dowolny komunikat z urządzenia do chmury wysyłany przez moduł przez niektóre lub Brak danych wyjściowych |
| `/messages/modules/<moduleId>/*` | Wszystkie komunikaty z urządzenia do chmury wysyłane przez określony moduł za pomocą niektórych lub braku danych wyjściowych |
| `/messages/modules/<moduleId>/outputs/*` | Wszystkie komunikaty z urządzenia do chmury wysyłane przez określony moduł za pomocą niektórych danych wyjściowych |
| `/messages/modules/<moduleId>/outputs/<output>` | Wszystkie komunikaty z urządzenia do chmury wysyłane przez określony moduł za pomocą określonych danych wyjściowych |

### <a name="condition"></a>Warunek

Warunek jest opcjonalny w deklaracji trasy. Jeśli chcesz przekazać wszystkie komunikaty ze źródła do ujścia, po prostu pozostaw klauzulę **WHERE** całkowicie. Można też użyć [języka zapytań IoT Hub](../iot-hub/iot-hub-devguide-routing-query-syntax.md) do filtrowania pewnych komunikatów lub typów komunikatów, które spełniają warunek. Trasy IoT Edge nie obsługują filtrowania komunikatów na podstawie znaczników lub właściwości bliźniaczych.

Komunikaty przekazywane między modułami w IoT Edge są sformatowane tak samo jak komunikaty przekazywane między urządzeniami i usługą Azure IoT Hub. Wszystkie komunikaty są sformatowane w formacie JSON i mają parametry **systemProperties**, **appProperties**i **Body** .

Można tworzyć zapytania dotyczące każdego z trzech parametrów z następującą składnią:

* Właściwości systemu: `$<propertyName>` lub `{$<propertyName>}`
* Właściwości aplikacji: `<propertyName>`
* Właściwości treści: `$body.<propertyName>`

Aby zapoznać się z przykładami dotyczącymi tworzenia zapytań dotyczących właściwości wiadomości, zobacz [wyrażenia zapytania dotyczącego urządzenia do chmury](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

Przykładem specyficznym dla IoT Edge jest przefiltrowanie komunikatów, które dotarły do urządzenia bramy z urządzenia typu liść. Komunikaty pochodzące z modułów obejmują Właściwość systemową o nazwie **connectionModuleId**. Dlatego jeśli chcesz skierować komunikaty z urządzeń liściowych bezpośrednio do IoT Hub, użyj następującej trasy, aby wykluczyć komunikaty modułu:

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Ujście

Ujścia definiuje, gdzie są wysyłane komunikaty. Tylko moduły i IoT Hub mogą odbierać wiadomości. Wiadomości nie mogą być kierowane do innych urządzeń. Właściwość ujścia nie zawiera żadnych opcji symboli wieloznacznych.

Właściwość ujścia może mieć jedną z następujących wartości:

| Ujście | Opis |
| ---- | ----------- |
| `$upstream` | Wyślij wiadomość do IoT Hub |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | Wyślij wiadomość do określonego wejścia do określonego modułu |

IoT Edge zapewnia gwarancje co najmniej jednokrotne. Centrum IoT Edge przechowuje komunikaty lokalnie na wypadek, gdyby trasa nie mogła dostarczyć wiadomości do ujścia. Na przykład jeśli Centrum IoT Edge nie może połączyć się z IoT Hub lub moduł docelowy nie jest połączony.

IoT Edge Hub przechowuje komunikaty do określonego czasu we `storeAndForwardConfiguration.timeToLiveSecs` właściwości [Centrum IoT Edge właściwości](module-edgeagent-edgehub.md).

### <a name="priority-and-time-to-live"></a>Priorytet i czas wygaśnięcia

Trasy można zadeklarować za pomocą tylko ciągu definiującego trasę lub jako obiekt, który pobiera ciąg tras, liczbę całkowitą o priorytecie i czas do wygaśnięcia.

Opcja 1.

   ```json
   "route1": "FROM <source> WHERE <condition> INTO <sink>",
   ```

Opcja 2 wprowadzona w IoT Edge wersja 1.0.10 ze schematem IoT Edge Hub w wersji 1,1:

   ```json
   "route2": {
     "route": "FROM <source> WHERE <condition> INTO <sink>",
     "priority": 0,
     "timeToLiveSecs": 86400
   }
   ```

**Priorytetowymi** wartościami mogą być 0-9, włącznie, gdzie 0 to najwyższy priorytet. Komunikaty są umieszczane w kolejce w oparciu o punkty końcowe. Wszystkie komunikaty o priorytecie 0 kierowane do określonego punktu końcowego zostaną przetworzone przed przetworzeniem wszystkich komunikatów o priorytecie 1 przeznaczonych dla tego samego punktu końcowego, a następnie wiersza. Jeśli wiele tras dla tego samego punktu końcowego ma ten sam priorytet, ich komunikaty będą przetwarzane w pierwszej kolejności. Jeśli priorytet nie zostanie określony, trasa zostanie przypisana do najniższego priorytetu.

Właściwość **timeToLiveSecs** dziedziczy wartość z **storeAndForwardConfiguration** Centrum IoT Edge, chyba że zostanie jawnie ustawiona. Wartość może być dowolną dodatnią liczbą całkowitą.

Aby uzyskać szczegółowe informacje na temat zarządzania kolejkami priorytetowymi, zobacz stronę referencyjną dla [priorytetu trasy i czasu wygaśnięcia](https://github.com/Azure/iotedge/blob/master/doc/Route_priority_and_TTL.md).

## <a name="define-or-update-desired-properties"></a>Definiowanie lub aktualizowanie żądanych właściwości

Manifest wdrożenia określa żądane właściwości dla każdego modułu wdrożonego na urządzeniu IoT Edge. Żądane właściwości w manifeście wdrożenia zastępują wszystkie wymagane właściwości, które są obecnie w postaci sznurka modułu.

Jeśli nie określisz odpowiednich właściwości sznurka modułu w manifeście wdrożenia, IoT Hub nie zmodyfikuje sznurka modułu w jakikolwiek sposób. Zamiast tego można ustawić odpowiednie właściwości programowo.

Te same mechanizmy, które umożliwiają modyfikowanie bliźniaczych reprezentacji urządzeń, służą do modyfikowania modułu bliźniaczych reprezentacji. Aby uzyskać więcej informacji, zobacz [Przewodnik dla deweloperów modułu bliźniaczy](../iot-hub/iot-hub-devguide-module-twins.md).

## <a name="deployment-manifest-example"></a>Przykład manifestu wdrażania

Poniższy przykład pokazuje, jak może wyglądać prawidłowy dokument manifestu wdrożenia.

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "ContosoRegistry": {
                "username": "myacr",
                "password": "<password>",
                "address": "myacr.azurecr.io"
              }
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 0,
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
            }
          }
        },
        "modules": {
          "SimulatedTemperatureSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 2,
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
            }
          },
          "filtermodule": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 1,
            "env": {
              "tempLimit": {"value": "100"}
            },
            "settings": {
              "image": "myacr.azurecr.io/filtermodule:latest",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "routes": {
          "sensorToFilter": {
            "route": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
            "priority": 0,
            "timeToLiveSecs": 1800
          },
          "filterToIoTHub": {
            "route": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream",
            "priority": 1,
            "timeToLiveSecs": 1800
          }
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 100
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać pełną listę właściwości, które mogą lub muszą być zawarte w $edgeAgent i $edgeHub, zobacz [Właściwości agenta IoT Edge i IoT Edge centrum](module-edgeagent-edgehub.md).

* Teraz, gdy wiesz już, jak są używane moduły IoT Edge, [Poznaj wymagania i narzędzia do opracowywania modułów IoT Edge](module-development.md).
