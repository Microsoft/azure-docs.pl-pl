---
title: Przewodnik publikowania dla ofert kontenerów w portalu Azure Marketplace
description: W tym artykule opisano wymagania dotyczące publikowania ofert kontenerów w portalu Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 11/30/2020
ms.openlocfilehash: 83c575aa40b80d9a8e39263e89a5e7860c8f8774
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95741665"
---
# <a name="publishing-guide-for-azure-container-offers"></a>Przewodnik publikowania dla ofert kontenerów platformy Azure

Usługa Azure Container oferuje pomoc w publikowaniu obrazu kontenera w witrynie Azure Marketplace. Skorzystaj z tego przewodnika, aby poznać wymagania dotyczące tego typu oferty.

Oferty kontenerów platformy Azure to oferty transakcji, które są wdrażane i rozliczane za pomocą witryny Azure Marketplace. Opcja aukcji wyświetlana przez użytkownika to "Pobierz go teraz".

Użyj typu oferty kontenera platformy Azure, gdy rozwiązanie jest obrazem kontenera Docker, który jest skonfigurowany jako Kubernetes Azure Container instance.

> [!NOTE]
> Wystąpienie kontenera platformy Azure to wystąpienie platformy Docker czasu wykonywania, które zapewnia najszybszy i najprostszy sposób uruchamiania kontenera na platformie Azure bez konieczności zarządzania wszystkimi maszynami wirtualnymi i bez konieczności wdrażania usługi wyższego poziomu. Wystąpienia kontenerów można wdrażać bezpośrednio na platformie Azure lub organizować przy użyciu usługi Azure Kubernetes Services lub aparatu usługi Azure Kubernetes Service.  

Firma Microsoft obsługuje obecnie bezpłatne modele licencjonowania (BYOL).

## <a name="container-offer-requirements"></a>Wymagania dotyczące oferty kontenera

| Wymaganie | Szczegóły |  
|:--- |:--- |  
| Rozliczenia i pomiary | Obsługuj model rozliczeń bezpłatny lub BYOL.<br><br> |  
| Obraz skompilowany na podstawie pliku dockerfile | Obrazy kontenerów muszą być oparte na specyfikacji obrazu platformy Docker i skonstruowane z pliku dockerfile.<br> <br>Więcej informacji o tworzeniu obrazów platformy Docker znajduje się w sekcji "użycie" w temacie [pliku dockerfile Reference](https://docs.docker.com/engine/reference/builder/#usage).<br><br> |  
| Hosting w repozytorium Azure Container Registry | Obrazy kontenerów muszą być hostowane w repozytorium Azure Container Registry.<br> <br>Aby uzyskać więcej informacji na temat pracy z Azure Container Registry, zobacz [Szybki Start: Tworzenie prywatnego rejestru kontenerów przy użyciu Azure Portal](../container-registry/container-registry-get-started-portal.md).<br><br> |  
| Tagowanie obrazów | Obrazy kontenerów muszą zawierać co najmniej jeden tag (maksymalną liczbę tagów: 16).<br><br>Aby uzyskać więcej informacji na temat tagowania obrazu, zobacz `docker tag` stronę w witrynie [platformy Docker](https://docs.docker.com/engine/reference/commandline/tag) .<br><br> |  

## <a name="next-steps"></a>Następne kroki

- Aby przygotować zasoby techniczne dla oferty kontenera, zobacz [Tworzenie zasobów technicznych kontenera platformy Azure](create-azure-container-technical-assets.md).

- Aby utworzyć ofertę platformy Azure, zapoznaj się z tematem [Tworzenie oferty kontenera platformy Azure w witrynie Azure Marketplace](create-azure-container-offer.md) , aby uzyskać więcej informacji.
