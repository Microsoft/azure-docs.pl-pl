---
title: Monitorowanie wdrożeń IoT Edge — Azure IoT Edge
description: Monitorowanie wysokiego poziomu, w tym edgeHub i edgeAgent raportowane właściwości i metryki wdrażania automatycznego.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/21/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 4ff4d5a810eb79fb11e66591cd0b695062b1c9f6
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91450179"
---
# <a name="monitor-iot-edge-deployments"></a>Monitorowanie wdrożeń IoT Edge

Azure IoT Edge udostępnia raportowanie umożliwiające monitorowanie w czasie rzeczywistym informacji o modułach wdrożonych na urządzeniach IoT Edge. Usługa IoT Hub Pobiera stan z urządzeń i udostępnia je operatorowi. Monitorowanie jest również ważne w przypadku [wdrożeń wykonanych na dużą skalę](module-deployment-monitoring.md) , które obejmują wdrożenia automatyczne i wdrożenia warstwowe.

Oba urządzenia i moduły mają podobne dane, takie jak łączność, dlatego wartości są uzyskiwane zgodnie z IDENTYFIKATORem urządzenia lub IDENTYFIKATORem modułu.

Usługa IoT Hub zbiera dane zgłoszone przez urządzenia i moduł bliźniaczych reprezentacji oraz zawiera liczbę różnych stanów, które mogą być dostępne dla urządzeń. Usługa IoT Hub organizuje te dane w czterech grupach metryk:

| Typ | Opis |
| --- | ---|
| Docelowe | Przedstawia IoT Edge urządzeń, które pasują do warunku określania wartości docelowej wdrożenia. |
| Zastosowano | Przedstawia IoT Edge urządzeń, które nie są objęte innym wdrożeniem o wyższym priorytecie. |
| Raportowanie sukcesu | Przedstawia IoT Edge urządzeń, które zgłosiły, że moduły zostały pomyślnie wdrożone. |
| Raportowanie niepowodzeń | Przedstawia IoT Edge urządzeń, które zgłosiły, że co najmniej jeden moduł nie został pomyślnie wdrożony. Aby dokładniej zbadać błąd, Połącz się zdalnie z tymi urządzeniami i Przejrzyj pliki dziennika. |

Usługa IoT Hub udostępnia te dane do monitorowania w Azure Portal i w interfejsie wiersza polecenia platformy Azure.

## <a name="monitor-a-deployment-in-the-azure-portal"></a>Monitorowanie wdrożenia w Azure Portal

Aby wyświetlić szczegóły wdrożenia i monitorować urządzenia, na których działa, wykonaj następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do IoT Hub.
1. W menu po lewej stronie wybierz pozycję **IoT Edge** .
1. Wybierz kartę **wdrożenia IoT Edge** .
1. Sprawdź listę wdrożenia.Dla każdego wdrożenia można wyświetlić następujące informacje:

    | Kolumna | Opis |
    | --- | --- |
    | ID | Nazwa wdrożenia. |
    | Typ | Typ **wdrożenia, wdrożenie lub** **wdrożenie warstwowe**. |
    | Warunek docelowy | Tag używany do definiowania urządzeń objętych usługą. |
    | Priorytet | Numer priorytetu przypisany do wdrożenia. |
    | Metryki systemu | Liczba bliźniaczych reprezentacji urządzeń w IoT Hub, które pasują do warunku określania wartości docelowej. **Zastosowano** określa liczbę urządzeń, na których zastosowana została zawartość wdrożenia do ich modułu bliźniaczych reprezentacji w IoT Hub. |
    | Metryki urządzeń | Liczba urządzeń IoT Edge zgłaszających sukcesy lub błędy w środowisku uruchomieniowym IoT Edge klienta. |
    | Metryki niestandardowe | Liczba urządzeń IoT Edge zgłaszających wszystkie metryki zdefiniowane dla wdrożenia. |
    | Czas utworzenia | Sygnatura czasowa od momentu utworzenia wdrożenia. Ta sygnatura czasowa jest używana do przerwania powiązań, gdy dwa wdrożenia mają ten sam priorytet. |

1. Wybierz wdrożenie, które chcesz monitorować.  
1. Na stronie **Szczegóły wdrożenia** przewiń w dół do dolnej sekcji i wybierz kartę **warunek docelowy** . Wybierz pozycję **Widok** , aby wyświetlić listę urządzeń, które pasują do warunku docelowego. Można zmienić warunek, a także **priorytet**. Jeśli wprowadzono zmiany, wybierz pozycję **Zapisz** .

   ![Wyświetlanie urządzeń przeznaczonych do wdrożenia](./media/how-to-monitor-iot-edge-deployments/target-devices.png)

1. Wybierz kartę **metryki** . W przypadku wybrania metryki z listy rozwijanej **Wybierz metrykę** zostanie wyświetlony przycisk **Wyświetl** , aby wyświetlić wyniki. Możesz również wybrać opcję **Edytuj metryki** , aby dostosować kryteria dla dowolnych metryk niestandardowych zdefiniowanych przez użytkownika. Jeśli wprowadzono zmiany, wybierz pozycję **Zapisz** .

   ![Wyświetlanie metryk dla wdrożenia](./media/how-to-monitor-iot-edge-deployments/deployment-metrics-tab.png)

Aby wprowadzić zmiany do wdrożenia, zobacz [Modyfikowanie wdrożenia](how-to-deploy-at-scale.md#modify-a-deployment).

## <a name="monitor-a-deployment-with-azure-cli"></a>Monitorowanie wdrożenia przy użyciu interfejsu wiersza polecenia platformy Azure

Użyj polecenia [az IoT Edge Deployment show](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-show) , aby wyświetlić szczegółowe informacje o pojedynczym wdrożeniu:

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

Polecenie Deployment show przyjmuje następujące parametry:

* **--Deployment-ID** — nazwa wdrożenia, która istnieje w usłudze IoT Hub. Parametr wymagany.
* **--Hub-Name** -Name Centrum IoT, w którym istnieje wdrożenie. Centrum musi znajdować się w bieżącej subskrypcji. Przejdź do żądanej subskrypcji za pomocą polecenia `az account set -s [subscription name]`

Sprawdź wdrożenie w oknie poleceń.Właściwość **metryki** zawiera liczbę dla każdej metryki ocenianej przez poszczególne centra:

* **targetedCount** — Metryka systemowa, która określa liczbę bliźniaczych reprezentacji urządzeń w IoT Hub, które pasują do warunku określania wartości docelowej.
* **appliedCount** — Metryka systemowa określa liczbę urządzeń, których zawartość wdrożenia została zastosowana do modułu bliźniaczych reprezentacji w IoT Hub.
* **reportedSuccessfulCount** — Metryka urządzenia, która określa liczbę IoT Edge urządzeń w ramach raportowania wdrożenia zakończonych powodzeniem z IoT Edge środowiska uruchomieniowego klienta.
* **reportedFailedCount** — Metryka urządzenia, która określa liczbę IoT Edge urządzeń w przypadku niepowodzenia raportowania wdrożenia w środowisku uruchomieniowym klienta IoT Edge.

Listę identyfikatorów urządzeń lub obiektów dla każdej z metryk można wyświetlić za pomocą polecenia [az IoT Edge Deployment show-Metric](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-show-metric) :

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name]
```

Polecenie show Deployment-Metric pobiera następujące parametry:

* **--Deployment-ID** — nazwa wdrożenia, która istnieje w usłudze IoT Hub.
* **--Metric-ID** -Nazwa metryki, dla której chcesz zobaczyć listę identyfikatorów urządzeń, na przykład `reportedFailedCount` .
* **--Hub-Name** -Name Centrum IoT, w którym istnieje wdrożenie. Centrum musi znajdować się w bieżącej subskrypcji. Przejdź do żądanej subskrypcji za pomocą polecenia `az account set -s [subscription name]` .

Aby wprowadzić zmiany do wdrożenia, zobacz [Modyfikowanie wdrożenia](how-to-deploy-cli-at-scale.md#modify-a-deployment).

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [monitorować bliźniaczych reprezentacji](how-to-monitor-module-twins.md)modułów, a przede wszystkim agentów IoT Edge i IoT Edge centrum środowiska uruchomieniowego w celu zapewnienia łączności i kondycji wdrożeń IoT Edge.
