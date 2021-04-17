---
title: Jak zainstalować aplikację na IoT Edge Kubernetes | Microsoft Docs
description: Dowiedz się, jak zainstalować środowisko IoT Edge Kubernetes przy użyciu lokalnego środowiska klastra projektowego
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: iotedge-2018-06
ms.openlocfilehash: a5407c46be6726974a920b5dddd87639dc426d0d
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587276"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Jak zainstalować aplikację na IoT Edge Kubernetes (wersja zapoznawcza)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

IoT Edge można zintegrować z platformą Kubernetes, używając jej jako odpornej, wysoce dostępnej warstwy infrastruktury. Oto miejsce, w którym ta obsługa pasuje do rozwiązania IoT Edge wysokiego poziomu:

![Wprowadzenie do k8s](./media/how-to-install-iot-edge-kubernetes/kubernetes-model.png)

>[!TIP]
>Dobrym modelem myślowym dla tej integracji jest pomyślenie o platformie Kubernetes jako o innym środowisku operacyjnym, IoT Edge w którym oprócz systemów Linux i Windows można uruchamiać aplikacje.

## <a name="architecture"></a>Architektura 
Na platformie Kubernetes IoT Edge *niestandardowej definicji* zasobów (CRD) dla wdrożeń obciążeń brzegowych. IoT Edge Agent przyjmuje rolę kontrolera  *CRD,* który uzgadnia żądany stan zarządzany przez chmurę ze stanem klastra lokalnego.

Okres istnienia modułu jest zarządzany przez harmonogram usługi Kubernetes, który zapewnia dostępność modułów i wybiera ich położenie. IoT Edge zarządza platformą aplikacji brzegowych, która działa na górze, stale uzgadnia żądany stan określony w IoT Hub ze stanem w klastrze krawędzi. Model aplikacji jest nadal znanym modelem opartym na IoT Edge modułów i tras. Kontroler IoT Edge Agent wykonuje  automatyczne IoT Edge modelu aplikacji usługi Kubernetes na konstrukcje natywne, takie jak zasobniki, wdrożenia, usługi itp.

Oto diagram architektury wysokiego poziomu:

![kubernetes arch](./media/how-to-install-iot-edge-kubernetes/publicpreview-refresh-kubernetes.png)

Każdy składnik wdrożenia brzegowego jest ograniczony do przestrzeni nazw kubernetes specyficznej dla urządzenia, co umożliwia współużytkowanie tych samych zasobów klastra między wieloma urządzeniami brzegowymi i ich wdrożeniami.

>[!NOTE]
>IoT Edge na kubernetes jest w publicznej [wersji zapoznawczej.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="tutorials-and-references"></a>Samouczki i odwołania 

Więcej informacji, w tym szczegółowe samouczki i odwołania, można znaleźć w witrynie z witrynie IoT Edge [kubernetes](https://aka.ms/edgek8sdoc) w wersji zapoznawczej.
