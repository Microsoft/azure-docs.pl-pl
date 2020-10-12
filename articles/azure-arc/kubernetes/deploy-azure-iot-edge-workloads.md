---
title: Wdrażanie obciążeń Azure IoT Edge (wersja zapoznawcza)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Wdrażanie obciążeń Azure IoT Edge
keywords: Kubernetes, łuk, Azure, K8s, kontenery
ms.openlocfilehash: bfaa43a03ddd98616b22fc3fc7b4dccb4c38f44c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86103980"
---
# <a name="deploy-azure-iot-edge-workloads-preview"></a>Wdrażanie obciążeń Azure IoT Edge (wersja zapoznawcza)

## <a name="overview"></a>Omówienie

Usługa Azure Arc i Azure IoT Edge uzupełniają wszystkie możliwości. Usługa Azure Arc udostępnia mechanizmy dla operatorów klastrów do konfigurowania podstawowych składników klastra oraz stosowania i wymuszania zasad klastra. IoT Edge umożliwia operatorom aplikacji zdalne wdrażanie obciążeń i zarządzanie nimi na dużą skalę przy użyciu wygody pozyskiwania chmurowego i komunikacji dwukierunkowej. Poniższy diagram ilustruje:

![Konfiguracja Arc IoT](./media/edge-arc.png)

## <a name="pre-requisites"></a>Wymagania wstępne

* [Zarejestruj urządzenie IoT Edge](../../iot-edge/quickstart-linux.md#register-an-iot-edge-device) i [Wdróż moduł symulowanej czujnika temperatury](../../iot-edge/quickstart-linux.md#deploy-a-module). Pamiętaj, aby zanotować parametry połączenia urządzenia.

* Użyj [obsługi IoT Edge](https://aka.ms/edgek8sdoc) , aby wdrożyć ją za pośrednictwem operatora strumienia usługi Azure Arc.

* Pobierz plik [**Values. yaml**](https://github.com/Azure/iotedge/blob/master/kubernetes/charts/edge-kubernetes/values.yaml) IoT Edge dla wykresu Helm i Zastąp symbol zastępczy **deviceConnectionString** na końcu pliku zanotowanym w kroku 1. W razie potrzeby można ustawić inne obsługiwane opcje instalacji wykresów. Utwórz przestrzeń nazw dla obciążenia IoT Edge i Utwórz wpis tajny:

    ```
    $ kubectl create ns iotedge

    $ kubectl create secret generic dcs --from-file=fully-qualified-path-to-values.yaml --namespace iotedge
    ```

    Można to również skonfigurować zdalnie przy użyciu [przykładu konfiguracji klastra](./use-gitops-connected-cluster.md).

## <a name="connect-a-cluster"></a>Łączenie klastra

Użyj `az` rozszerzenia interfejsu wiersza polecenia, `connectedk8s` Aby połączyć klaster Kubernetes z usługą Azure ARC:

  ```
  az connectedk8s connect --name AzureArcIotEdge --resource-group AzureArcTest
  ```

## <a name="create-a-configuration-for-iot-edge"></a>Utwórz konfigurację dla IoT Edge

Przykładowe repozytorium: https://github.com/veyalla/edgearc

To repozytorium wskazuje IoT Edge wykres Helm i odwołuje się do wpisu tajnego utworzonego w sekcji wymagania wstępne.

1. Użyj `az` rozszerzenia interfejsu wiersza polecenia, `k8sconfiguration` Aby utworzyć konfigurację służącą do łączenia połączonego klastra z repozytorium git:

    ```
    az k8sconfiguration create --name iotedge --cluster-name AzureArcIotEdge --resource-group AzureArcTest --operator-instance-name iotedge --operator-namespace azure-arc-iot-edge --enable-helm-operator --helm-operator-chart-version 0.6.0 --helm-operator-chart-values "--set helm.versions=v3" --repository-url "git://github.com/veyalla/edgearc.git" --cluster-scoped
    ```

    W ciągu minuty lub dwóch powinny zostać wyświetlone IoT Edge moduły obciążenia wdrożone w `iotedge` przestrzeni nazw w klastrze. Możesz wyświetlić dzienniki w `SimulatedTemperatureSensor` tym obszarze nazw, aby zobaczyć przykładowe wartości, które są generowane. Możesz również obejrzeć komunikaty docierające do centrum IoT Hub przy użyciu [rozszerzenia zestawu Azure IoT Hub Toolkit dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="cleanup"></a>Czyszczenie

Konfigurację można usunąć przy użyciu:

```
az k8sconfiguration delete -g AzureArcTest --cluster-name AzureArcIotEdge --name iotedge
```

## <a name="next-steps"></a>Następne kroki

[Zarządzanie konfiguracją klastra przy użyciu Azure Policy](./use-azure-policy.md)
