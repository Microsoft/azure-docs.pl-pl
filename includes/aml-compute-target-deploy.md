---
title: plik dołączany
description: plik dołączany
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 06/25/2020
ms.openlocfilehash: 944b96e7726f2b2becd5960a17a89c00d00c878a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91841962"
---
Obiekt docelowy obliczeń używany do hostowania modelu będzie miał wpływ na koszt i dostępność wdrożonego punktu końcowego. Użyj tej tabeli, aby wybrać odpowiedni element docelowy obliczeń.

| Docelowy zasób obliczeniowy | Sposób użycia | Obsługa procesora GPU | Obsługa FPGA | Opis |
| ----- | ----- | ----- | ----- | ----- |
| [Lokalna &nbsp; Usługa sieci Web &nbsp;](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | Testowanie/debugowanie | &nbsp; | &nbsp; | Służy do ograniczonego testowania i rozwiązywania problemów. Przyspieszenie sprzętowe zależy od użycia bibliotek w systemie lokalnym.
| [&nbsp;Usługa sieci Web wystąpienia obliczeniowego Azure Machine Learning &nbsp;](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | Testowanie/debugowanie | &nbsp; | &nbsp; | Służy do ograniczonego testowania i rozwiązywania problemów.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/how-to-deploy-azure-kubernetes-service.md) | Wnioskowanie w czasie rzeczywistym |  [Tak](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (wdrożenie usługi sieci Web) | [Tak](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Używany w przypadku wdrożeń produkcyjnych o dużej skali. Zapewnia krótki czas odpowiedzi i automatyczne skalowanie wdrożonej usługi. Skalowanie automatyczne klastra nie jest obsługiwane za pomocą zestawu SDK Azure Machine Learning. Aby zmienić węzły w klastrze AKS, użyj interfejsu użytkownika klastra AKS w Azure Portal. AKS jest jedyną opcją dostępną dla projektanta. |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-azure-container-instance.md) | Testowanie lub programowanie | &nbsp;  | &nbsp; | Używany w przypadku obciążeń opartych na PROCESORAch o niskiej skali, które wymagają mniej niż 48 GB pamięci RAM. |
| [Klaster obliczeniowy usługi Azure Machine Learning](../articles/machine-learning/how-to-use-parallel-run-step.md) | &nbsp;Wnioskowanie partii | [Tak](../articles/machine-learning/how-to-use-parallel-run-step.md) (potok Machine Learning) | &nbsp;  | Uruchom ocenianie wsadowe w przypadku obliczeń bezserwerowych. Obsługuje maszyny wirtualne o normalnym i niskim priorytecie. |
| [Azure Functions](../articles/machine-learning/how-to-deploy-functions.md) | Przeglądania Wnioskowanie w czasie rzeczywistym | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/how-to-deploy-and-where.md#iotedge) | Przeglądania &nbsp;Moduł IoT |  &nbsp; | &nbsp; | Wdrażaj i obsługuj modele uczenia maszynowego na urządzeniach IoT. |
| [Azure Data Box Edge](../articles/databox-online/azure-stack-edge-overview.md)   | Za pomocą usługi IoT Edge |  &nbsp; | Tak | Wdrażaj i obsługuj modele uczenia maszynowego na urządzeniach IoT. |

> [!NOTE]
> Chociaż obiekty docelowe obliczeń, takie jak lokalne, Azure Machine Learning obliczeniowe i Azure Machine Learning, obsługują procesor GPU na potrzeby szkoleń i eksperymentowania, użycie procesora GPU do wnioskowania w przypadku _wdrożenia jako usługi sieci Web_ jest obsługiwane tylko w AKS.
>
> Użycie procesora GPU do wnioskowania, _gdy ocenianie z potokiem uczenia maszynowego_ jest obsługiwane tylko na Azure Machine Learning obliczeń.

> [!NOTE]
> * Wystąpienia kontenera są odpowiednie tylko w przypadku małych modeli o rozmiarze mniejszym niż 1 GB.
> * Używaj jednowęzłowych klastrów AKS na potrzeby tworzenia i testowania większych modeli.