---
title: Przewodnik publikowania ofert kontenerów dla portalu Azure Marketplace
description: W tym artykule opisano wymagania dotyczące publikowania kontenerów w portalu Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 80bcf0d5d37a44cee2aab38161abac619542204a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684320"
---
# <a name="containers-offer-publishing-guide"></a>Przewodnik publikowania oferty kontenerów

Oferty kontenerów ułatwią publikowanie obrazu kontenera w portalu Azure Marketplace. Skorzystaj z tego przewodnika, aby zrozumieć wymagania dotyczące tej oferty. 

Są to oferty transakcji, które są wdrażane i rozliczane za pośrednictwem portalu Marketplace. Wywołanie działania, które użytkownik widzi jest "Get It Now".

Użyj typu oferty kontenera, gdy rozwiązanie jest obraz kontenera platformy Docker aprowizowana jako usługa kontenera platformy Azure oparta na usłudze Platformy Azure opartej na usłudze Kubernetes.

>[!NOTE]
>Na przykład usługa kontenera platformy Azure oparta na usłudze platformy Azure oparta na usłudze platformy Kubernetes, taka jak usługa Azure Kubernetes Service lub Wystąpienia kontenerów platformy Azure, wybór klientów platformy Azure dla środowiska wykonawczego kontenera opartego na usłudze Kubernetes.  

Firma Microsoft obsługuje obecnie modele licencjonowania bezpłatnej i własnej licencji (BYOL).

## <a name="containers-offer"></a>Oferta kontenerów

| Wymaganie | Szczegóły |  
|:--- |:--- |  
| Rozliczenia i pomiary | Obsługa bezpłatnego lub byolowego modelu rozliczeniowego. |  
| Obraz zbudowany z pliku Dockerfile | Obrazy kontenerów muszą być oparte na specyfikacji obrazu platformy Docker i muszą być zbudowane z pliku Dockerfile.<ul> <li>Aby uzyskać więcej informacji na temat tworzenia obrazów dok [docs.docker.com/engine/reference/builder/#usage.](https://docs.docker.com/engine/reference/builder/#usage)</li> </ul> |  
| Hosting w ACR | Obrazy kontenerów muszą być hostowane w repozytorium rejestru kontenerów platformy Azure (ACR).<ul> <li>Aby uzyskać więcej informacji na temat pracy z usługą ACR, odwiedź przewodnik Szybki start: Tworzenie rejestru kontenerów przy użyciu strony portalu Azure znajdującej się w [docs.microsoft.com/azure/container-registry/container-registry-get-started-portal](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal).</li> </ul> |  
| Tagowanie obrazów | Obrazy kontenerów muszą zawierać co najmniej 1 znacznik (maksymalna liczba tagów: 16).<ul> <li>Aby uzyskać więcej informacji na temat oznaczania obrazu, odwiedź stronę tagu docker znajdującą się w [docs.docker.com/engine/reference/commandline/tag](https://docs.docker.com/engine/reference/commandline/tag).</li> </ul> |  

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiłeś, 

- [Dowiedz się więcej](https://azuremarketplace.microsoft.com/sell) o rynku.

Aby zarejestrować się w Centrum partnerów, rozpocznij tworzenie nowej oferty lub pracę nad istniejącą:

- [Zaloguj się do Centrum partnerów,](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) aby utworzyć lub uzupełnić ofertę.
- Aby uzyskać więcej [informacji, zobacz tworzenie oferty kontenerów platformy Azure.](./partner-center-portal/create-azure-container-offer.md)
