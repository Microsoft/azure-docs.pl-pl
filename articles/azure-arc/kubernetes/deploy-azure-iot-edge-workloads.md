---
title: Wdrażanie obciążeń Azure IoT Edge
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Wdrażanie obciążeń Azure IoT Edge
keywords: Kubernetes, łuk, Azure, K8s, kontenery
ms.openlocfilehash: e77446170e5a6adac995394d66640fd183f453b8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121732"
---
# <a name="deploy-azure-iot-edge-workloads"></a>Wdrażanie obciążeń Azure IoT Edge

## <a name="overview"></a>Omówienie

Usługa Azure Arc i Azure IoT Edge łatwo uzupełniać możliwości. 

Usługa Azure Arc udostępnia mechanizmy operatorów klastrów umożliwiające konfigurowanie podstawowych składników klastra i stosowanie i wymuszanie zasad klastra. 

Azure IoT Edge umożliwia operatorom aplikacji zdalne wdrażanie obciążeń i zarządzanie nimi na dużą skalę przy użyciu wygody pozyskiwania chmurowego i dwukierunkowego komunikacji. 

Na poniższym diagramie przedstawiono relację usługi Azure Arc i Azure IoT Edge:

![Konfiguracja Arc IoT](./media/edge-arc.png)

## <a name="pre-requisites"></a>Wymagania wstępne

* [Zarejestruj urządzenie IoT Edge](../../iot-edge/quickstart-linux.md#register-an-iot-edge-device) i [Wdróż moduł symulowanej czujnika temperatury](../../iot-edge/quickstart-linux.md#deploy-a-module). Zanotuj parametry połączenia urządzenia dla *wartości. YAML* wymienione poniżej.

* Użyj [obsługi IoT Edge](https://aka.ms/edgek8sdoc) , aby wdrożyć ją za pośrednictwem operatora strumienia usługi Azure Arc.

* Pobierz plik [*Values. yaml*](https://github.com/Azure/iotedge/blob/preview/iiot/kubernetes/charts/edge-kubernetes/values.yaml) IoT Edge dla wykresu Helm i Zastąp `deviceConnectionString` symbol zastępczy na końcu pliku parametrami połączenia zanotowanymi wcześniej. W razie konieczności Ustaw inne obsługiwane opcje instalacji wykresów. Utwórz przestrzeń nazw dla obciążenia IoT Edge i Wygeneruj klucz tajny:

  ```
  $ kubectl create ns iotedge

  $ kubectl create secret generic dcs --from-file=fully-qualified-path-to-values.yaml --namespace iotedge
  ```

  Możesz również skonfigurować zdalnie za pomocą [przykładu konfiguracji klastra](./tutorial-use-gitops-connected-cluster.md).

## <a name="connect-a-cluster"></a>Łączenie klastra

Użyj `az` rozszerzenia interfejsu wiersza polecenia platformy Azure, `connectedk8s` Aby połączyć klaster Kubernetes z usługą Azure ARC:

  ```
  az connectedk8s connect --name AzureArcIotEdge --resource-group AzureArcTest
  ```

## <a name="create-a-configuration-for-iot-edge"></a>Utwórz konfigurację dla IoT Edge

[Przykładowe repozytorium git](https://github.com/veyalla/edgearc) wskazuje na wykres IoT Edge Helm i odwołuje się do wpisu tajnego utworzonego w sekcji wymagania wstępne.

Użyj `az` rozszerzenia interfejsu wiersza polecenia platformy Azure, `k8s-configuration` Aby utworzyć konfigurację łączącą połączony klaster z repozytorium git:

  ```
  az k8s-configuration create --name iotedge --cluster-name AzureArcIotEdge --resource-group AzureArcTest --operator-instance-name iotedge --operator-namespace azure-arc-iot-edge --enable-helm-operator --helm-operator-chart-version 0.6.0 --helm-operator-chart-values "--set helm.versions=v3" --repository-url "git://github.com/veyalla/edgearc.git" --cluster-scoped
  ```

W ciągu kilku minut powinny zostać wyświetlone IoT Edge moduły obciążenia wdrożone w `iotedge` przestrzeni nazw klastra. 

Wyświetl `SimulatedTemperatureSensor` dzienniki pod tej przestrzeni nazw, aby zobaczyć przykładowe wartości, które są generowane. Możesz również obejrzeć komunikaty docierające do centrum IoT Hub przy użyciu [rozszerzenia zestawu Azure IoT Hub Toolkit dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="cleanup"></a>Czyszczenie

Usuń konfigurację przy użyciu:

```
az k8s-configuration delete -g AzureArcTest --cluster-name AzureArcIotEdge --name iotedge
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [za pomocą Azure Policy zarządzać konfiguracją klastra](./use-azure-policy.md).
