---
title: Jak zainstalować aplikację na IoT Edge Kubernetes | Microsoft Docs
description: Dowiedz się, jak zainstalować środowisko IoT Edge kubernetes przy użyciu lokalnego środowiska klastra projektowego
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: iotedge-2018-06
ms.openlocfilehash: 1c7c221a2fea60f3bbbc4f2cde960dcb8638efe2
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576571"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Jak zainstalować aplikację na IoT Edge Kubernetes (wersja zapoznawcza)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge można zintegrować z platformą Kubernetes, używając jej jako odpornej, wysoce dostępnej warstwy infrastruktury. Oto, gdzie ta obsługa pasuje do rozwiązania wysokiego IoT Edge poziomie:

![Wprowadzenie do k8s](./media/how-to-install-iot-edge-kubernetes/kubernetes-model.png)

>[!TIP]
>Dobrym modelem mentalnym dla tej integracji jest pomyślenie o platformie Kubernetes jako o innym środowisku operacyjnym, IoT Edge w którym oprócz systemów Linux i Windows można uruchamiać aplikacje.

## <a name="architecture"></a>Architektura 
Na platformie Kubernetes IoT Edge *niestandardowej* definicji zasobów (CRD) dla wdrożeń obciążeń brzegowych. IoT Edge agent przyjmuje rolę kontrolera  *CRD,* który uzgadnia żądany stan zarządzany przez chmurę ze stanem klastra lokalnego.

Okres istnienia modułu jest zarządzany przez harmonogram kubernetes, który zapewnia dostępność modułów i wybiera ich położenie. IoT Edge zarządza platformą aplikacji brzegowej uruchomionej na górze, stale uzgadniając żądany stan określony w IoT Hub ze stanem w klastrze krawędzi. Model aplikacji jest nadal znanym modelem opartym na IoT Edge modułów i tras. Kontroler IoT Edge Agent wykonuje  automatyczne IoT Edge modelu aplikacji usługi Kubernetes na konstrukcje natywne, takie jak zasobniki, wdrożenia, usługi itp.

Oto diagram architektury wysokiego poziomu:

![arch kubernetes](./media/how-to-install-iot-edge-kubernetes/publicpreview-refresh-kubernetes.png)

Każdy składnik wdrożenia brzegowego jest ograniczony do przestrzeni nazw kubernetes specyficznej dla urządzenia, co umożliwia współużytkowanie tych samych zasobów klastra między wieloma urządzeniami brzegowym i ich wdrożeniami.

>[!NOTE]
>IoT Edge na kubernetes jest w publicznej [wersji zapoznawczej.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="tutorials-and-references"></a>Samouczki i odwołania 

Aby uzyskać więcej informacji, w tym szczegółowe samouczki i odwołania, zobacz witrynę z IoT Edge [kubernetes](https://aka.ms/edgek8sdoc) w wersji zapoznawczej.
