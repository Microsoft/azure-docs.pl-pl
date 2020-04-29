---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/23/2019
ms.openlocfilehash: ba1e0eee5de65364d8e5e762195f7de98384c2a7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76122591"
---
| Docelowy zasób obliczeniowy | Sposób użycia | Obsługa procesora GPU | Obsługa FPGA | Opis |
| ----- | ----- | ----- | ----- | ----- |
| [Lokalna&nbsp;usługa&nbsp;sieci Web](../articles/machine-learning/how-to-deploy-and-where.md#local) | Testowanie/debugowanie | &nbsp; | &nbsp; | Służy do ograniczonego testowania i rozwiązywania problemów. Przyspieszenie sprzętowe zależy od użycia bibliotek w systemie lokalnym.
| [Usługa sieci Web&nbsp;&nbsp;wystąpienia obliczeniowego Azure Machine Learning](../articles/machine-learning/how-to-deploy-and-where.md#notebookvm) | Testowanie/debugowanie | &nbsp; | &nbsp; | Służy do ograniczonego testowania i rozwiązywania problemów.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/how-to-deploy-and-where.md#aks) | Wnioskowanie w czasie rzeczywistym |  [Tak](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (wdrożenie usługi sieci Web) | [Tak](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Używany w przypadku wdrożeń produkcyjnych o dużej skali. Zapewnia krótki czas odpowiedzi i automatyczne skalowanie wdrożonej usługi. Skalowanie automatyczne klastra nie jest obsługiwane za pomocą zestawu SDK Azure Machine Learning. Aby zmienić węzły w klastrze AKS, użyj interfejsu użytkownika klastra AKS w Azure Portal. AKS jest jedyną opcją dostępną dla projektanta. |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-and-where.md#aci) | Testowanie lub programowanie | &nbsp;  | &nbsp; | Używany w przypadku obciążeń opartych na PROCESORAch o niskiej skali, które wymagają mniej niż 48 GB pamięci RAM. |
| [Klaster obliczeniowy usługi Azure Machine Learning](../articles/machine-learning/how-to-use-parallel-run-step.md) | Przeglądania Wnioskowanie partii&nbsp; | [Tak](../articles/machine-learning/how-to-use-parallel-run-step.md) (potok Machine Learning) | &nbsp;  | Uruchom ocenianie wsadowe w przypadku obliczeń bezserwerowych. Obsługuje maszyny wirtualne o normalnym i niskim priorytecie. |
| [Azure Functions](../articles/machine-learning/how-to-deploy-functions.md) | Przeglądania Wnioskowanie w czasie rzeczywistym | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/how-to-deploy-and-where.md#iotedge) | Przeglądania Moduł&nbsp;IoT |  &nbsp; | &nbsp; | Wdrażanie i obsługiwanie modeli ML na urządzeniach IoT. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | Za pośrednictwem IoT Edge |  &nbsp; | Tak | Wdrażanie i obsługiwanie modeli ML na urządzeniach IoT. |

> [!NOTE]
> Mimo że obiekty docelowe obliczeń, takie jak lokalne, Azure Machine Learning wystąpienia obliczeniowe i Azure Machine Learning klastrów obliczeniowych, obsługują procesor GPU do szkoleń i eksperymentów, użycie procesora GPU do wnioskowania w __przypadku wdrożenia jako usługi sieci Web__ jest obsługiwane tylko w usłudze Azure Kubernetes.
>
> Użycie procesora GPU do wnioskowania, __gdy ocenianie z potokiem uczenia maszynowego__ jest obsługiwane tylko na Azure Machine Learning obliczeń.