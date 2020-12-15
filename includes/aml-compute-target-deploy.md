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
ms.date: 12/11/2020
ms.openlocfilehash: 09dd6e9a9d69797c2c33270d1620e861a052efe2
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505124"
---
Obiekt docelowy obliczeń używany do hostowania modelu będzie miał wpływ na koszt i dostępność wdrożonego punktu końcowego. Użyj tej tabeli, aby wybrać odpowiedni element docelowy obliczeń.

| Docelowy zasób obliczeniowy | Sposób użycia | Obsługa procesora GPU | Obsługa FPGA | Opis |
| ----- | ----- | ----- | ----- | ----- |
| [Lokalna &nbsp; Usługa sieci Web &nbsp;](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | Testowanie/debugowanie | &nbsp; | &nbsp; | Służy do ograniczonego testowania i rozwiązywania problemów. Przyspieszenie sprzętowe zależy od użycia bibliotek w systemie lokalnym.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/how-to-deploy-azure-kubernetes-service.md) | Wnioskowanie w czasie rzeczywistym |  [Tak](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (wdrożenie usługi sieci Web) | [Tak](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Używany w przypadku wdrożeń produkcyjnych o dużej skali. Zapewnia krótki czas odpowiedzi i automatyczne skalowanie wdrożonej usługi. Skalowanie automatyczne klastra nie jest obsługiwane za pomocą zestawu SDK Azure Machine Learning. Aby zmienić węzły w klastrze AKS, użyj interfejsu użytkownika klastra AKS w Azure Portal. <br/><br/> Obsługiwane przez projektanta. |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-azure-container-instance.md) | Testowanie lub programowanie | &nbsp;  | &nbsp; | Używany w przypadku obciążeń opartych na PROCESORAch o niskiej skali, które wymagają mniej niż 48 GB pamięci RAM. <br/><br/> Obsługiwane przez projektanta. |
| [Klaster obliczeniowy usługi Azure Machine Learning](../articles/machine-learning/tutorial-pipeline-batch-scoring-classification.md) | &nbsp;Wnioskowanie partii | [Tak](../articles/machine-learning/tutorial-pipeline-batch-scoring-classification.md) (potok Machine Learning) | &nbsp;  | Uruchom ocenianie wsadowe w przypadku obliczeń bezserwerowych. Obsługuje maszyny wirtualne o normalnym i niskim priorytecie. Brak obsługi wnioskowania o czasie rzeczywistym.|

> [!NOTE]
> Chociaż obiekty docelowe obliczeń, takie jak lokalne, Azure Machine Learning obliczeniowe i Azure Machine Learning, obsługują procesor GPU na potrzeby szkoleń i eksperymentowania, użycie procesora GPU do wnioskowania w przypadku _wdrożenia jako usługi sieci Web_ jest obsługiwane tylko w AKS.
>
> Użycie procesora GPU do wnioskowania, _gdy ocenianie z potokiem uczenia maszynowego_ jest obsługiwane tylko na Azure Machine Learning obliczeń.
> 
> Podczas wybierania jednostki SKU klastra najpierw Skaluj w górę, a następnie Skaluj w poziomie. Zacznij od komputera, który ma 150% pamięci RAM wymaganej przez model, Profiluj wynik i Znajdź maszynę o wymaganej wydajności. Po uzyskaniu informacji o tym Zwiększ liczbę maszyn, aby dopasować ją do potrzeb współbieżności.

> [!NOTE]
> * Wystąpienia kontenera są odpowiednie tylko w przypadku małych modeli o rozmiarze mniejszym niż 1 GB.
> * Używaj jednowęzłowych klastrów AKS na potrzeby tworzenia i testowania większych modeli.