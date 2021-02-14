---
title: Wdrażanie modułów na dużą skalę przy użyciu interfejsu wiersza polecenia platformy Azure — Azure IoT Edge
description: Tworzenie wdrożeń automatycznych dla grup IoT Edge urządzeń przy użyciu rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 4ecb1c3dc0e72523b19e3183e17306774b3ce164
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370380"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Wdrażanie i monitorowanie modułów IoT Edge na dużą skalę przy użyciu interfejsu wiersza polecenia platformy Azure

Utwórz **IoT Edge Automatyczne wdrażanie** przy użyciu interfejsu wiersza polecenia platformy Azure do zarządzania trwającymi wdrożeniami dla wielu urządzeń jednocześnie. Automatyczne wdrożenia dla IoT Edge są częścią funkcji [automatycznej zarządzania urządzeniami](../iot-hub/iot-hub-automatic-device-management.md) w programie IoT Hub. Wdrożenia to procesy dynamiczne, które umożliwiają wdrożenie wielu modułów na wielu urządzeniach, śledzenie stanu i kondycji modułów oraz wprowadzanie zmian w razie potrzeby.

Aby uzyskać więcej informacji, zobacz [opis IoT Edge wdrożenia automatyczne dla pojedynczych urządzeń lub w odpowiedniej skali](module-deployment-monitoring.md).

W tym artykule opisano konfigurowanie interfejsu wiersza polecenia platformy Azure i rozszerzenia IoT. Następnie dowiesz się, jak wdrażać moduły na zestawie IoT Edge urządzeń i monitorować postęp przy użyciu dostępnych poleceń interfejsu wiersza polecenia.

## <a name="prerequisites"></a>Wymagania wstępne

* [Centrum IoT](../iot-hub/iot-hub-create-using-cli.md) w ramach subskrypcji platformy Azure.
* Co najmniej jedno urządzenie IoT Edge.

  Jeśli nie masz skonfigurowanego urządzenia IoT Edge, możesz je utworzyć na maszynie wirtualnej platformy Azure. Wykonaj kroki opisane w jednym z artykułów z przewodnikiem Szybki Start, aby [utworzyć wirtualne urządzenie](quickstart-linux.md) z systemem Linux lub [utworzyć wirtualne urządzenie systemu Windows](quickstart.md).

* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w Twoim środowisku. Minimalna wersja interfejsu wiersza polecenia platformy Azure musi być 2.0.70 lub nowsza. Użyj polecenia `az --version` w celu przeprowadzenia weryfikacji. Ta wersja obsługuje polecenia rozszerzenia az i wprowadza platformę poleceń Knack.
* [Rozszerzenie IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Konfigurowanie manifestu wdrożenia

Manifest wdrożenia to dokument JSON, który opisuje moduły do wdrożenia, sposób przepływu danych między modułami i żądane właściwości modułu bliźniaczych reprezentacji. Aby uzyskać więcej informacji, zobacz [Informacje o sposobie wdrażania modułów i ustanawiania tras w programie IoT Edge](module-composition.md).

Aby wdrożyć moduły przy użyciu interfejsu wiersza polecenia platformy Azure, Zapisz manifest wdrożenia lokalnie jako plik txt. Ścieżka do pliku jest używana w następnej sekcji po uruchomieniu polecenia, aby zastosować konfigurację na urządzeniu.

Oto podstawowy manifest wdrożenia z jednym modułem:

>[!NOTE]
>Ten przykładowy manifest wdrażania używa wersji schematu 1,1 dla agenta IoT Edge i centrum. Wersja schematu 1,1 została wydana wraz z IoT Edge wersja 1.0.10 i włącza funkcje takie jak kolejność uruchamiania modułu i priorytetyzacja tras.

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.1",
          "runtime": {
            "type": "docker",
            "settings": {
              "minDockerVersion": "v1.25",
              "loggingOptions": "",
              "registryCredentials": {}
            }
          },
          "systemModules": {
            "edgeAgent": {
              "type": "docker",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
                "createOptions": "{}"
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "SimulatedTemperatureSensor": {
              "version": "1.1",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                "createOptions": "{}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

## <a name="layered-deployment"></a>Wdrożenie warstwowe

Wdrożenia warstwowe są typem automatycznego wdrażania, które mogą być ułożone między sobą. Aby uzyskać więcej informacji na temat wdrożeń warstwowych, zobacz [opis IoT Edge wdrożenia automatyczne dla pojedynczych urządzeń lub w odpowiedniej skali](module-deployment-monitoring.md).

Wdrożenia warstwowe można tworzyć i zarządzać nimi za pomocą interfejsu wiersza polecenia platformy Azure, takiego jak dowolne wdrożenie automatyczne, za pomocą zaledwie kilku różnic. Po utworzeniu wdrożenia warstwowego ten sam interfejs wiersza polecenia platformy Azure działa w przypadku wdrożeń warstwowych w taki sam sposób, jak w przypadku każdego wdrożenia. Aby utworzyć wdrożenie warstwowe, Dodaj `--layered` flagę do polecenia CREATE.

Druga różnica polega na przygotowaniu manifestu wdrożenia. Chociaż standardowe wdrożenie automatyczne musi zawierać moduły środowiska uruchomieniowego systemu oprócz modułów użytkownika, wdrożenia warstwowe mogą zawierać tylko moduły użytkownika. Zamiast tego wdrożenia warstwowe wymagają standardowego wdrożenia automatycznego również na urządzeniu, aby dostarczyć wymagane składniki każdego IoT Edge urządzenia, takie jak moduły środowiska uruchomieniowego systemu.

Poniżej przedstawiono podstawowy manifest wdrożenia warstwowego z jednym modułem na przykład:

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired.modules.SimulatedTemperatureSensor": {
          "settings": {
            "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": ""
          },
          "type": "docker",
          "status": "running",
          "restartPolicy": "always",
          "version": "1.0"
        }
      },
      "$edgeHub": {
        "properties.desired.routes.upstream": "FROM /messages/* INTO $upstream"
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

W poprzednim przykładzie pokazano ustawienie wdrożenia warstwowego `properties.desired` dla modułu. Jeśli to wdrożenie warstwowe nadano urządzeniu, w którym już zastosowano ten sam moduł, spowoduje to zastąpienie wszelkich istniejących żądanych właściwości. Aby można było zaktualizować zamiast zastępować odpowiednie właściwości, możesz zdefiniować nową podsekcję. Na przykład:

```json
"SimulatedTEmperatureSensor": {
  "properties.desired.layeredProperties": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

Aby uzyskać więcej informacji o konfigurowaniu modułu bliźniaczych reprezentacji w przypadku wdrożeń warstwowych, zobacz [wdrażanie warstwowe](module-deployment-monitoring.md#layered-deployment)

## <a name="identify-devices-using-tags"></a>Identyfikowanie urządzeń przy użyciu tagów

Przed utworzeniem wdrożenia należy mieć możliwość określania, które urządzenia mają mieć wpływ. Azure IoT Edge identyfikuje urządzenia przy użyciu **tagów** z sznurka urządzenia. Każde urządzenie może mieć wiele tagów zdefiniowanych w dowolny sposób, który ma sens dla danego rozwiązania. Na przykład w przypadku zarządzania najwyższego budynku można dodać następujące znaczniki do urządzenia:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Aby uzyskać więcej informacji na temat bliźniaczych reprezentacji i tagów urządzeń, zobacz [Omówienie i używanie urządzenia bliźniaczych reprezentacji w IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Tworzenie wdrożenia

Wdrażasz moduły na urządzeniach docelowych, tworząc wdrożenie, które składa się z manifestu wdrożenia, a także innych parametrów.

Użyj polecenia [AZ IoT Edge Deployment Create](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-create) , aby utworzyć wdrożenie:

```azurecli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

Użyj tego samego polecenia z `--layered` flagą, aby utworzyć wdrożenie warstwowe.

Polecenie wdrożenia Create przyjmuje następujące parametry:

* **--warstwowy** — opcjonalna flaga identyfikująca wdrożenie jako wdrożenie warstwowe.
* **--Deployment-ID** — nazwa wdrożenia, które zostanie utworzone w usłudze IoT Hub. Nadaj wdrożenie unikatową nazwę, która jest maksymalnie 128 małymi literami. Unikaj spacji i następujących nieprawidłowych znaków: `& ^ [ ] { } \ | " < > /` . Parametr wymagany.
* **--Content** -FilePath do pliku JSON manifestu wdrożenia. Parametr wymagany.
* **--Hub-Name** -Name Centrum IoT, w którym zostanie utworzone wdrożenie. Centrum musi znajdować się w bieżącej subskrypcji. Zmień bieżącą subskrypcję za pomocą `az account set -s [subscription name]` polecenia.
* **--etykiety** — Dodawanie etykiet w celu ułatwienia śledzenia wdrożeń. Etykiety to nazwy i pary wartości opisujące wdrożenie. Etykiety przyjmują formatowanie JSON dla nazw i wartości. Na przykład `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--Target-Condition** -wprowadź warunek docelowy, aby określić, które urządzenia będą ukierunkowane na to wdrożenie. Warunek jest oparty na tagach bliźniaczych urządzeń lub w raportowanych właściwościach urządzenia i powinien być zgodny z formatem wyrażenia. Na przykład `tags.environment='test' and properties.reported.devicemodel='4000x'`.
* **--Priority** -dodatnia liczba całkowita. W przypadku, gdy co najmniej dwa wdrożenia są przeznaczone dla tego samego urządzenia, stosowane będzie wdrożenie o najwyższej wartości liczbowej dla priorytetu.
* **--Metrics** — Utwórz metryki, które wysyłają zapytania do edgeHub raportowanych właściwości, aby śledzić stan wdrożenia. Metryki pobierają dane wejściowe JSON lub ścieżki. Na przykład `'{"queries": {"mymetric": "SELECT deviceId FROM devices WHERE properties.reported.lastDesiredStatus.code = 200"}}'`.

Aby monitorować wdrożenie przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [monitorowanie wdrożeń IoT Edge](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-with-azure-cli).

## <a name="modify-a-deployment"></a>Modyfikowanie wdrożenia

Po zmodyfikowaniu wdrożenia zmiany są natychmiast replikowane na wszystkie urządzenia objęte usługą.

W przypadku zaktualizowania warunku docelowego następujące aktualizacje są wykonywane:

* Jeśli urządzenie nie spełnia starego warunku docelowego, ale spełnia warunki nowego elementu docelowego, a to wdrożenie ma najwyższy priorytet dla tego urządzenia, to wdrożenie jest stosowane na urządzeniu.
* Jeśli urządzenie, na którym jest uruchomione to wdrożenie, już nie spełnia warunku docelowego, Odinstalowuje to wdrożenie i podejmuje kolejne wdrożenie o najwyższym priorytecie.
* Jeśli urządzenie, na którym jest uruchomione to wdrożenie, już nie spełnia warunku docelowego i nie spełnia warunku docelowego innych wdrożeń, nie nastąpi żadne zmiany na urządzeniu. Urządzenie kontynuuje uruchamianie bieżących modułów w bieżącym stanie, ale nie jest już zarządzane w ramach tego wdrożenia. Po spełnieniu warunku docelowego dowolnego innego wdrożenia Odinstalowuje to wdrożenie i przyjmuje nowe.

Nie można zaktualizować zawartości wdrożenia, w tym modułów i tras zdefiniowanych w manifeście wdrożenia. Aby zaktualizować zawartość wdrożenia, należy utworzyć nowe wdrożenie, które jest przeznaczone dla tych samych urządzeń o wyższym priorytecie. Można modyfikować pewne właściwości istniejącego modułu, w tym warunek docelowy, etykiety, metryki i priorytet.

Użyj polecenia [AZ IoT Edge Deployment Update](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-update) , aby zaktualizować wdrożenie:

```azurecli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

Polecenie aktualizacji wdrożenia przyjmuje następujące parametry:

* **--Deployment-ID** — nazwa wdrożenia, która istnieje w usłudze IoT Hub.
* **--Hub-Name** -Name Centrum IoT, w którym istnieje wdrożenie. Centrum musi znajdować się w bieżącej subskrypcji. Przejdź do żądanej subskrypcji za pomocą polecenia `az account set -s [subscription name]`
* **--Set** -aktualizuje właściwość we wdrożeniu. Można zaktualizować następujące właściwości:
  * targetCondition — na przykład `targetCondition=tags.location.state='Oregon'`
  * Etykieta
  * priority
* **--Add** -Dodaj nową właściwość do wdrożenia, łącznie z warunkami docelowymi lub etykietami.
* **--Remove** -Usuwa istniejącą właściwość, włącznie z warunkami docelowymi lub etykietami.

## <a name="delete-a-deployment"></a>Usuwanie wdrożenia

Po usunięciu wdrożenia wszystkie urządzenia przyjmą kolejne wdrożenie o najwyższym priorytecie. Jeśli urządzenia nie spełniają warunku docelowego innego wdrożenia, moduły nie zostaną usunięte po usunięciu wdrożenia.

Użyj polecenia [AZ IoT Edge Deployment Delete](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-delete) , aby usunąć wdrożenie:

```azurecli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name]
```

Polecenie usunięcia wdrożenia przyjmuje następujące parametry:

* **--Deployment-ID** — nazwa wdrożenia, która istnieje w usłudze IoT Hub.
* **--Hub-Name** -Name Centrum IoT, w którym istnieje wdrożenie. Centrum musi znajdować się w bieżącej subskrypcji. Przejdź do żądanej subskrypcji za pomocą polecenia `az account set -s [subscription name]`

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [wdrażaniu modułów na IoT Edge urządzeniach](module-deployment-monitoring.md).