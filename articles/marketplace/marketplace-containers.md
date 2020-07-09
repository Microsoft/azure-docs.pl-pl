---
title: Przewodnik publikowania dla ofert kontenerów w portalu Azure Marketplace
description: W tym artykule opisano wymagania dotyczące publikowania ofert kontenerów w portalu Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 04/15/2020
ms.openlocfilehash: 81069b0340202a0a481522ef1920806026ebcbe1
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119399"
---
# <a name="publishing-guide-for-container-offers"></a>Przewodnik publikowania dla ofert kontenera

Kontener oferuje pomoc w publikowaniu obrazu kontenera w witrynie Azure Marketplace. Skorzystaj z tego przewodnika, aby poznać wymagania dotyczące tej oferty. 

Oferty kontenerów to oferty transakcji, które są wdrażane i rozliczane za pomocą witryny Azure Marketplace. Wywołanie akcji, którą widzi użytkownik, to "Pobierz teraz".

Użyj typu oferty *kontenera* , gdy rozwiązanie jest obrazem kontenera Docker, który jest skonfigurowany jako wystąpienie usługi kontenera platformy Azure opartej na Kubernetes. 

> [!NOTE]
> Przykłady wystąpień usługi kontenera platformy Azure opartej na Kubernetes to usługa Azure Kubernetes Service lub Azure Container Instances, wybór klientów platformy Azure dla środowiska uruchomieniowego kontenera opartego na Kubernetes.  

Firma Microsoft obsługuje obecnie bezpłatne modele licencjonowania (BYOL).

## <a name="container-offer-requirements"></a>Wymagania dotyczące oferty kontenera

| Wymaganie | Szczegóły |  
|:--- |:--- |  
| Rozliczenia i pomiary | Obsługuj model rozliczeń bezpłatny lub BYOL.<br><br> |  
| Obraz skompilowany na podstawie pliku dockerfile | Obrazy kontenerów muszą być oparte na specyfikacji obrazu platformy Docker i skonstruowane z pliku dockerfile.<br> <br>Więcej informacji o tworzeniu obrazów platformy Docker znajduje się w sekcji "użycie" w temacie [pliku dockerfile Reference](https://docs.docker.com/engine/reference/builder/#usage).<br><br> |  
| Hosting w repozytorium Azure Container Registry | Obrazy kontenerów muszą być hostowane w repozytorium Azure Container Registry.<br> <br>Aby uzyskać więcej informacji na temat pracy z Azure Container Registry, zobacz [Szybki Start: Tworzenie prywatnego rejestru kontenerów przy użyciu Azure Portal](../container-registry/container-registry-get-started-portal.md).<br><br> |  
| Tagowanie obrazów | Obrazy kontenerów muszą zawierać co najmniej jeden tag (maksymalną liczbę tagów: 16).<br><br>Aby uzyskać więcej informacji na temat tagowania obrazu, zobacz `docker tag` stronę w witrynie [platformy Docker](https://docs.docker.com/engine/reference/commandline/tag) .<br><br> |  

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiono, Dowiedz się, jak [rozwijać swoją firmę w chmurze za pomocą witryny Azure Marketplace](https://azuremarketplace.microsoft.com/sell).

Aby zarejestrować się i rozpocząć pracę w centrum partnerskim:

- [Zaloguj się do Centrum partnerskiego](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) , aby utworzyć lub zakończyć swoją ofertę.
- Aby uzyskać więcej informacji, zobacz [Tworzenie oferty kontenera platformy Azure](./partner-center-portal/create-azure-container-offer.md) .
