---
title: Jak zainstalować IoT Edge na Kubernetes | Microsoft Docs
description: Informacje na temat instalowania IoT Edge na Kubernetes przy użyciu lokalnego środowiska klastra projektowego
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d5fc7b6b480056b56a2776cebd0fa87a5b96f9f0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201670"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Jak zainstalować IoT Edge w Kubernetes (wersja zapoznawcza)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge można zintegrować z usługą Kubernetes przy użyciu jej jako odpornej warstwy infrastruktury o wysokiej dostępności. Oto, gdzie ta obsługa pasuje do IoT Edge rozwiązania wysokiego poziomu:

![Wprowadzenie do k8s](./media/how-to-install-iot-edge-kubernetes/kubernetes-model.png)

>[!TIP]
>Dobrym modelem psychicznym dla tej integracji jest Kubernetes, ponieważ inne środowisko operacyjne IoT Edge aplikacji, a nie tylko dla systemów Linux i Windows.

## <a name="architecture"></a>Architektura 
W systemie Kubernetes IoT Edge udostępnia *niestandardowe definicje zasobów* (CRD) dla wdrożeń obciążeń brzegowych. Agent IoT Edge przyjmuje rolę  *kontrolera CRD* , który uzgadnia żądany stan zarządzany przez chmurę ze stanem lokalnego klastra.

Okres istnienia modułu jest zarządzany przez usługę Kubernetes Scheduler, która zachowuje dostępność modułu i wybiera ich rozmieszczenie. IoT Edge zarządza platformą aplikacji brzegowej działającą na górze, nieustannie uzgadniając żądany stan określony w IoT Hub ze stanem w klastrze brzegowym. Model aplikacji nadal jest znanym modelem opartym na IoT Edge modułach i trasach. Kontroler agenta IoT Edge wykonuje *Automatyczne* tłumaczenie IoT Edge modelu aplikacji na natywne konstrukcje Kubernetes, takie jak zasobniki, wdrożenia, usługi itd.

Oto diagram architektury wysokiego poziomu:

![Kubernetes Arch](./media/how-to-install-iot-edge-kubernetes/publicpreview-refresh-kubernetes.png)

Każdy składnik wdrożenia brzegowego jest objęty zakresem przestrzeni nazw Kubernetes specyficzne dla urządzenia, dzięki czemu możliwe jest udostępnianie tych samych zasobów klastra między wieloma urządzeniami brzegowymi i ich wdrożeniami.

>[!NOTE]
>IoT Edge w Kubernetes jest w [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="tutorials-and-references"></a>Samouczki i materiały referencyjne 

Aby uzyskać więcej informacji, w tym szczegółowe samouczki i odwołania, zobacz [IoT Edge w witrynie Kubernetes Preview w wersji zapoznawczej](https://aka.ms/edgek8sdoc) .
