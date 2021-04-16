---
title: Monitorowanie IoT Edge wdrożenia — Azure IoT Edge
description: Monitorowanie wysokiego poziomu, w tym zgłaszane właściwości edgeHub i edgeAgent oraz metryki automatycznego wdrażania.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/21/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 39e7bb5c151d490e79ef111589f52f260c3e6c7a
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483167"
---
# <a name="monitor-iot-edge-deployments"></a>Monitorowanie wdrożeń IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge raporty, które umożliwiają monitorowanie informacji o modułach wdrożonych na urządzeniach IoT Edge w czasie rzeczywistym. Usługa IoT Hub pobiera stan z urządzeń i udostępnia je operatorowi. Monitorowanie jest również ważne w przypadku wdrożeń na dużą [skalę,](module-deployment-monitoring.md) które obejmują wdrożenia automatyczne i wdrożenia warstwowe.

Zarówno urządzenia, jak i moduły mają podobne dane, takie jak łączność, więc wartości są uzyskiwane zgodnie z identyfikatorem urządzenia lub identyfikatorem modułu.

Usługa IoT Hub zbiera dane zgłaszane przez bliźniacze reprezentacji urządzenia i modułu oraz podaje liczbę różnych stanów, które mogą mieć urządzenia. Usługa IoT Hub organizuje te dane w cztery grupy metryk:

| Typ | Opis |
| --- | ---|
| Docelowe | Przedstawia IoT Edge urządzeń, które pasują do warunku określania wartości docelowej wdrożenia. |
| Zastosowano | Przedstawia docelowe IoT Edge, które nie są celem innego wdrożenia o wyższym priorytecie. |
| Raportowanie powodzenia | Przedstawia IoT Edge, które zgłosiły pomyślne wdrożenie modułów. |
| Błąd raportowania | Przedstawia IoT Edge, które zgłosiły, że co najmniej jeden moduł nie został pomyślnie wdrożony. Aby dokładniej zbadać błąd, połącz się zdalnie z tymi urządzeniami i wyświetl pliki dziennika. |

Usługa IoT Hub udostępnia te dane do monitorowania w witrynie Azure Portal interfejsie wiersza polecenia platformy Azure.

## <a name="monitor-a-deployment-in-the-azure-portal"></a>Monitorowanie wdrożenia w Azure Portal

Aby wyświetlić szczegóły wdrożenia i monitorować uruchomione na nim urządzenia, należy wykonać następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do swojego IoT Hub.
1. Wybierz **IoT Edge** z menu okienka po lewej stronie.
1. Wybierz **kartę IoT Edge wdrożenia.**
1. Sprawdź listę wdrożeń. Dla każdego wdrożenia można wyświetlić następujące szczegóły:

    | Kolumna | Opis |
    | --- | --- |
    | ID (Identyfikator) | Nazwa wdrożenia. |
    | Typ | Typ wdrożenia: Wdrożenie **lub** **Wdrożenie warstwowe.** |
    | Warunek docelowy | Tag używany do definiowania urządzeń docelowych. |
    | Priorytet | Numer priorytetu przypisany do wdrożenia. |
    | Metryki systemu | Liczba bliźniaczych reprezentacji urządzeń w IoT Hub, które pasują do warunku określania wartości docelowej. **Zastosowane** określa liczbę urządzeń, na których zastosowano zawartość wdrożenia do bliźniaczych reprezentacji modułów w IoT Hub. |
    | Metryki urządzeń | Liczba urządzeń IoT Edge zgłaszających powodzenie lub błędy ze środowiska uruchomieniowego IoT Edge klienta. |
    | Metryki niestandardowe | Liczba urządzeń IoT Edge raportuje dane dla wszystkich metryk zdefiniowanych dla wdrożenia. |
    | Czas tworzenia | Sygnatura czasowa od czasu utworzenia wdrożenia. Ten znacznik czasu służy do łamania powiązania, gdy dwa wdrożenia mają ten sam priorytet. |

1. Wybierz wdrożenie, które chcesz monitorować.  
1. Na stronie **Szczegóły wdrożenia** przewiń w dół do dolnej sekcji i wybierz **kartę Warunek** docelowy. Wybierz **pozycję Widok,** aby wyświetlić listę urządzeń, które pasują do warunku docelowego. Można zmienić warunek, a także **priorytet**. Wybierz **pozycję Zapisz,** jeśli zostały wprowadzone zmiany.

   ![Wyświetlanie urządzeń docelowych dla wdrożenia](./media/how-to-monitor-iot-edge-deployments/target-devices.png)

1. Wybierz **kartę Metryki.** Jeśli wybierzesz metrykę z listy **rozwijanej** Wybierz metrykę, zostanie **wyświetlony przycisk** Wyświetl, aby wyświetlić wyniki. Możesz również wybrać pozycję **Edytuj metryki,** aby dostosować kryteria dla dowolnych metryk niestandardowych, które zostały zdefiniowane. Wybierz **pozycję Zapisz,** jeśli zostały wprowadzone zmiany.

   ![Wyświetlanie metryk dla wdrożenia](./media/how-to-monitor-iot-edge-deployments/deployment-metrics-tab.png)

Aby wprowadzić zmiany we wdrożeniu, zobacz [Modyfikowanie wdrożenia](how-to-deploy-at-scale.md#modify-a-deployment).

## <a name="monitor-a-deployment-with-azure-cli"></a>Monitorowanie wdrożenia za pomocą interfejsu wiersza polecenia platformy Azure

Użyj polecenia [az iot edge deployment show,](/cli/azure/iot/edge/deployment) aby wyświetlić szczegóły pojedynczego wdrożenia:

```azurecli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

Polecenie deployment show przyjmuje następujące parametry:

* **--deployment-id** — nazwa wdrożenia, które istnieje w centrum IoT. Parametr wymagany.
* **--hub-name** — nazwa centrum IoT, w którym istnieje wdrożenie. Koncentrator musi znajdować się w bieżącej subskrypcji. Przejdź do żądanej subskrypcji za pomocą polecenia `az account set -s [subscription name]`

Sprawdź wdrożenie w oknie polecenia. Właściwość **metrics** zawiera liczbę dla każdej metryki ocenianej przez poszczególne centra:

* **targetedCount** — metryka systemu określająca liczbę bliźniaczych reprezentacji urządzenia w IoT Hub, które pasują do warunku określania wartości docelowej.
* **appliedCount** — metryka systemu określa liczbę urządzeń, na których zastosowano zawartość wdrożenia do bliźniaczych reprezentacji modułów w IoT Hub.
* **reportedSuccessfulCount** — metryka urządzenia określająca liczbę urządzeń IoT Edge we wdrożeniu zgłaszających powodzenie z IoT Edge uruchomieniowego klienta.
* **reportedFailedCount** — metryka urządzenia określająca liczbę urządzeń IoT Edge w przypadku niepowodzenia raportowania wdrożenia ze środowiska uruchomieniowego IoT Edge klienta.

Listę identyfikatorów urządzeń lub obiektów dla każdej metryki można wyświetlić za pomocą polecenia [az iot edge deployment show-metric:](/cli/azure/iot/edge/deployment)

```azurecli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name]
```

Polecenie show-metric wdrożenia przyjmuje następujące parametry:

* **--deployment-id** — nazwa wdrożenia, które istnieje w centrum IoT.
* **--metric-id** — nazwa metryki, dla której chcesz wyświetlić listę identyfikatorów urządzeń, na przykład `reportedFailedCount` .
* **--hub-name** — nazwa centrum IoT, w którym istnieje wdrożenie. Koncentrator musi znajdować się w bieżącej subskrypcji. Przejdź do żądanej subskrypcji za pomocą polecenia `az account set -s [subscription name]` .

Aby wprowadzić zmiany we wdrożeniu, zobacz [Modyfikowanie wdrożenia](how-to-deploy-cli-at-scale.md#modify-a-deployment).

## <a name="next-steps"></a>Następne kroki

Dowiedz się, [jak](how-to-monitor-module-twins.md)monitorować bliźniacze reprezentacji modułów , przede wszystkim moduły środowiska uruchomieniowego IoT Edge Agent i IoT Edge Hub, aby uzyskać łączność i kondycję IoT Edge wdrożeń.
