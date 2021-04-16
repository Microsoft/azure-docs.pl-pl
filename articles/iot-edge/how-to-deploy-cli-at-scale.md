---
title: Wdrażanie modułów na dużą skalę przy użyciu interfejsu wiersza polecenia platformy Azure — Azure IoT Edge
description: Używanie rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure do tworzenia automatycznych wdrożeń dla grup IoT Edge urządzeń
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: c502a9c02160c5a92d78ccdbb0532e6f173122da
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479512"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Wdrażanie i monitorowanie modułów IoT Edge na dużą skalę przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Utwórz automatyczne **IoT Edge przy** użyciu interfejsu wiersza polecenia platformy Azure, aby zarządzać bieżącymi wdrożeniami dla wielu urządzeń jednocześnie. Automatyczne wdrożenia dla IoT Edge są częścią funkcji [automatycznego](../iot-hub/iot-hub-automatic-device-management.md) zarządzania urządzeniami usługi IoT Hub. Wdrożenia to procesy dynamiczne, które umożliwiają wdrażanie wielu modułów na wielu urządzeniach, śledzenie stanu i kondycji modułów oraz w razie potrzeby wprowadzać zmiany.

Aby uzyskać więcej informacji, zobacz Understand IoT Edge automatic deployments for single devices or at scale (Opis wdrażania automatycznego dla [pojedynczych urządzeń lub na dużą skalę).](module-deployment-monitoring.md)

W tym artykule skonfigurujemy interfejs wiersza polecenia platformy Azure i rozszerzenie IoT. Następnie dowiesz się, jak wdrażać moduły na zestawie IoT Edge i monitorować postęp przy użyciu dostępnych poleceń interfejsu wiersza polecenia.

## <a name="prerequisites"></a>Wymagania wstępne

* Centrum [IoT w](../iot-hub/iot-hub-create-using-cli.md) ramach subskrypcji platformy Azure.
* Co najmniej jedno IoT Edge urządzenia.

  Jeśli nie masz urządzenia IoT Edge, możesz je utworzyć na maszynie wirtualnej platformy Azure. Wykonaj kroki opisane w jednym z artykułów Szybki start, aby [utworzyć wirtualne](quickstart-linux.md) urządzenie z systemem Linux lub utworzyć wirtualne urządzenie z systemem [Windows.](quickstart.md)

* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w Twoim środowisku. Interfejs wiersza polecenia platformy Azure musi mieć co najmniej wersję 2.0.70 lub nowszą. Użyj polecenia `az --version` w celu przeprowadzenia weryfikacji. Ta wersja obsługuje polecenia rozszerzenia az i wprowadza platformę poleceń Knack.
* Rozszerzenie [IoT dla interfejsu wiersza polecenia platformy Azure.](https://github.com/Azure/azure-iot-cli-extension)

## <a name="configure-a-deployment-manifest"></a>Konfigurowanie manifestu wdrożenia

Manifest wdrożenia to dokument JSON, który opisuje moduły do wdrożenia, sposób przepływu danych między modułami i żądane właściwości bliźniaczych reprezentacji modułów. Aby uzyskać więcej informacji, zobacz [Dowiedz się, jak wdrażać moduły i ustanawiać trasy w IoT Edge](module-composition.md).

Aby wdrożyć moduły przy użyciu interfejsu wiersza polecenia platformy Azure, zapisz manifest wdrożenia lokalnie jako plik txt. Ścieżka pliku w następnej sekcji zostanie użycia podczas uruchamiania polecenia w celu zastosowania konfiguracji do urządzenia.

Oto podstawowy manifest wdrożenia z jednym modułem jako przykładem:

>[!NOTE]
>Ten przykładowy manifest wdrożenia używa wersji schematu 1.1 dla IoT Edge i centrum. Wersja schematu 1.1 została wydana wraz z programem IoT Edge w wersji 1.0.10 i udostępnia funkcje, takie jak kolejność uruchamiania modułów i określanie priorytetów tras.

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

## <a name="layered-deployment"></a>Wdrażanie warstwowe

Wdrożenia warstwowe to typ automatycznego wdrażania, który może być skumulowany na siebie nawzajem. Aby uzyskać więcej informacji na temat wdrożeń warstwowych, zobacz Understand IoT Edge automatic deployments for single devices or at scale (Opis wdrażania automatycznego dla pojedynczych urządzeń lub [na dużą skalę).](module-deployment-monitoring.md)

Wdrożenia warstwowe można tworzyć i zarządzać nimi za pomocą interfejsu wiersza polecenia platformy Azure, tak jak każde wdrożenie automatyczne, z zaledwie kilkoma różnicami. Po utworzeniu wdrożenia warstwowego ten sam interfejs wiersza polecenia platformy Azure działa w przypadku wdrożeń warstwowych tak samo jak każde wdrożenie. Aby utworzyć wdrożenie warstwowe, dodaj `--layered` flagę do polecenia create.

Druga różnica polega na konstrukcji manifestu wdrożenia. Chociaż standardowe wdrożenie automatyczne musi zawierać moduły środowiska uruchomieniowego systemu oprócz modułów użytkownika, wdrożenia warstwowe mogą zawierać tylko moduły użytkownika. Zamiast tego wdrożenia warstwowe wymagają standardowego automatycznego wdrożenia również na urządzeniu, aby dostarczać wymagane składniki każdego urządzenia IoT Edge, takie jak moduły systemowego środowiska uruchomieniowego.

Oto podstawowy warstwowy manifest wdrożenia z jednym modułem na przykład:

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

W poprzednim przykładzie popisaliśmy się warstwowym ustawieniem wdrożenia `properties.desired` dla modułu. Jeśli to wdrożenie warstwowe dotyczyło urządzenia, na którym ten sam moduł został już zastosowany, zastąpiłoby wszystkie istniejące żądane właściwości. Aby zaktualizować żądane właściwości zamiast zastępowania, można zdefiniować nową podsekcję. Na przykład:

```json
"SimulatedTEmperatureSensor": {
  "properties.desired.layeredProperties": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

Aby uzyskać więcej informacji na temat konfigurowania bliźniaczych reprezentacji modułów we wdrożeniach [warstwowych,](module-deployment-monitoring.md#layered-deployment) zobacz Wdrażanie warstwowe

## <a name="identify-devices-using-tags"></a>Identyfikowanie urządzeń przy użyciu tagów

Przed utworzeniem wdrożenia należy określić, na które urządzenia chcesz wpłynąć. Azure IoT Edge urządzenia przy użyciu **tagów w** bliźniaczej reprezentacji urządzenia. Każde urządzenie może mieć wiele tagów, które definiujesz w dowolny sposób, który ma sens dla twojego rozwiązania. Jeśli na przykład zarządzasz kampusem inteligentnych budynków, możesz dodać następujące tagi do urządzenia:

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

Aby uzyskać więcej informacji na temat bliźniaczych reprezentacji urządzeń i tagów, zobacz [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md)(Informacje na temat bliźniaczych reprezentacji urządzeń i ich IoT Hub).

## <a name="create-a-deployment"></a>Tworzenie wdrożenia

Moduły są wdrażane na urządzeniach docelowych przez utworzenie wdrożenia, które składa się z manifestu wdrożenia oraz innych parametrów.

Użyj polecenia [az iot edge deployment create,](/cli/azure/iot/edge/deployment) aby utworzyć wdrożenie:

```azurecli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

Użyj tego samego polecenia z `--layered` flagą , aby utworzyć wdrożenie warstwowe.

Polecenie deployment create przyjmuje następujące parametry:

* **--layered** — opcjonalna flaga identyfikująca wdrożenie jako wdrożenie warstwowe.
* **--deployment-id** — nazwa wdrożenia, które zostanie utworzone w centrum IoT. Nadaj wdrożeniem unikatową nazwę, która może mieć maksymalnie 128 małych liter. Unikaj spacji i następujących nieprawidłowych znaków: `& ^ [ ] { } \ | " < > /` . Parametr wymagany.
* **--content** — ścieżka pliku do pliku JSON manifestu wdrożenia. Parametr wymagany.
* **--hub-name** — nazwa centrum IoT Hub, w którym zostanie utworzone wdrożenie. Koncentrator musi znajdować się w bieżącej subskrypcji. Zmień bieżącą subskrypcję za pomocą `az account set -s [subscription name]` polecenia .
* **--labels** — dodaj etykiety, aby ułatwić śledzenie wdrożeń. Etykiety to pary Nazwa i Wartość, które opisują wdrożenie. Etykiety mają formatowanie JSON dla nazw i wartości. Na przykład `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--target-condition** — wprowadź warunek docelowy, aby określić, które urządzenia będą celem tego wdrożenia. Warunek jest oparty na tagach bliźniaczej reprezentacji urządzenia lub zgłaszanych właściwościach bliźniaczej reprezentacji urządzenia i powinien być zgodne z formatem wyrażenia. Na przykład `tags.environment='test' and properties.reported.devicemodel='4000x'`.
* **--priority** — dodatnia liczba całkowita. W przypadku, gdy co najmniej dwa wdrożenia są celem tego samego urządzenia, będzie stosowane wdrożenie o najwyższej wartości liczbowej priorytetu.
* **--metrics** — utwórz metryki, które odpytujące właściwości zgłaszane przez usługę edgeHub, aby śledzić stan wdrożenia. Metryki mają dane wejściowe JSON lub ścieżka pliku. Na przykład `'{"queries": {"mymetric": "SELECT deviceId FROM devices WHERE properties.reported.lastDesiredStatus.code = 200"}}'`.

Aby monitorować wdrożenie przy użyciu interfejsu wiersza polecenia platformy Azure, [zobacz IoT Edge wdrożenia.](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-with-azure-cli)

## <a name="modify-a-deployment"></a>Modyfikowanie wdrożenia

Po zmodyfikowaniu wdrożenia zmiany są natychmiast replikowane do wszystkich urządzeń docelowych.

Jeśli zaktualizujemy warunek docelowy, nastąpią następujące aktualizacje:

* Jeśli urządzenie nie spełnia starego warunku docelowego, ale spełnia nowy warunek docelowy, a to wdrożenie ma najwyższy priorytet dla tego urządzenia, to wdrożenie zostanie zastosowane do urządzenia.
* Jeśli urządzenie aktualnie uruchomione w tym wdrożeniu nie spełnia już warunku docelowego, odinstaluje to wdrożenie i przejmuje następne wdrożenie o najwyższym priorytecie.
* Jeśli urządzenie aktualnie uruchomione w tym wdrożeniu nie spełnia już warunku docelowego i nie spełnia warunku docelowego innych wdrożeń, na urządzeniu nie nastąpi żadna zmiana. Urządzenie kontynuuje działanie bieżących modułów w bieżącym stanie, ale nie jest już zarządzane w ramach tego wdrożenia. Gdy program spełnia warunek docelowy każdego innego wdrożenia, odinstalowuje to wdrożenie i przejmuje nowe wdrożenie.

Nie można zaktualizować zawartości wdrożenia, w tym modułów i tras zdefiniowanych w manifeście wdrożenia. Jeśli chcesz zaktualizować zawartość wdrożenia, możesz to zrobić, tworząc nowe wdrożenie, które będzie dotyczyć tych samych urządzeń o wyższym priorytecie. Możesz zmodyfikować niektóre właściwości istniejącego modułu, w tym warunek docelowy, etykiety, metryki i priorytet.

Użyj polecenia [az iot edge deployment update,](/cli/azure/iot/edge/deployment) aby zaktualizować wdrożenie:

```azurecli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

Polecenie aktualizacji wdrożenia przyjmuje następujące parametry:

* **--deployment-id** — nazwa wdrożenia, które istnieje w centrum IoT.
* **--hub-name** — nazwa centrum IoT, w którym istnieje wdrożenie. Koncentrator musi znajdować się w bieżącej subskrypcji. Przejdź do żądanej subskrypcji za pomocą polecenia `az account set -s [subscription name]`
* **--set** — aktualizowanie właściwości we wdrożeniu. Możesz zaktualizować następujące właściwości:
  * targetCondition — na przykład `targetCondition=tags.location.state='Oregon'`
  * Etykiety
  * priority
* **--add** — dodaj nową właściwość do wdrożenia, w tym warunki docelowe lub etykiety.
* **--remove** — usuwa istniejącą właściwość, w tym warunki lub etykiety docelowe.

## <a name="delete-a-deployment"></a>Usuwanie wdrożenia

Po usunięciu wdrożenia wszystkie urządzenia przejmują kolejne wdrożenie o najwyższym priorytecie. Jeśli urządzenia nie spełniają warunku docelowego żadnego innego wdrożenia, moduły nie zostaną usunięte po usunięciu wdrożenia.

Użyj polecenia [az iot edge deployment delete,](/cli/azure/iot/edge/deployment) aby usunąć wdrożenie:

```azurecli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name]
```

Polecenie usunięcia wdrożenia przyjmuje następujące parametry:

* **--deployment-id** — nazwa wdrożenia, które istnieje w centrum IoT.
* **--hub-name** — nazwa centrum IoT, w którym istnieje wdrożenie. Centrum musi znajdować się w bieżącej subskrypcji. Przejdź do żądanej subskrypcji za pomocą polecenia `az account set -s [subscription name]`

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [wdrażaniu modułów na IoT Edge urządzeniach.](module-deployment-monitoring.md)