---
title: Oferta obrazu kontenerów platformy Azure | Portal Azure Marketplace
description: Przegląd procesu publikowania oferty kontenera w witrynie Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: dsindona
ms.openlocfilehash: 81908de5c2fb3960684ed6cf37952e8815d8a5d5
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82148311"
---
# <a name="containers"></a>Containers

> [!IMPORTANT]
> Od 13 kwietnia 2020 rozpocznie się zarządzanie ofertami kontenerów platformy Azure w centrum partnerskim. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [Tworzenie oferty kontenera platformy Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer) , aby zarządzać migrowanymi ofertami.

<table> <tr> <td>W tej sekcji opisano sposób publikowania obrazu kontenera w <a href="https://azuremarketplace.microsoft.com">witrynie Azure Marketplace</a>.  
Typ oferty kontenera obsługuje obrazy kontenerów platformy Docker udostępniane jako wystąpienia <a href="https://docs.microsoft.com/azure/aks/index">usługi Azure Kubernetes</a> lub <a href="https://docs.microsoft.com/azure/container-instances/container-instances-overview">Azure Container Instances</a> i hostowane w repozytorium <a href="https://docs.microsoft.com/azure/container-registry">Azure Container Registry</a> . </td> <td><img src="./media/container-icon.png"  alt="Azure container icon" /></td> </tr> </table>

## <a name="offer-components"></a>Składniki oferty

Ta sekcja zawiera opis elementów publikowania kontenera i jest przeznaczona dla wydawców w portalu Azure Marketplace. Publikowanie jest podzielone na następujące główne części:

- [Wymagania wstępne](./cpp-prerequisites.md) — przed utworzeniem lub opublikowaniem oferty kontenera należy spełnić wymagania techniczne i biznesowe.
- [Tworzenie oferty](./cpp-create-offer.md) — Lista czynności wymaganych do utworzenia nowego wpisu oferty kontenera przy użyciu Portal Cloud partner.
- [Przygotuj zasoby techniczne](./cpp-create-technical-assets.md) — jak utworzyć zasoby techniczne dla rozwiązania kontenera jako ofertę w portalu Azure Marketplace.
- [Opublikowanie oferty](./cpp-publish-offer.md) — przesyłanie oferty do opublikowania w portalu Azure Marketplace.

## <a name="container-publishing-process"></a>Proces publikowania kontenera

Na poniższym diagramie przedstawiono ogólne kroki publikowania oferty maszyn wirtualnych.
![Kroki umożliwiające opublikowanie oferty](./media/containers-offer-process.png)

Ogólne kroki publikowania oferty kontenera są następujące:

1. Utwórz ofertę — podaj szczegółowe informacje o ofercie. Te informacje obejmują: opis oferty, materiały marketingowe, informacje o pomocy technicznej i specyfikacje zasobów.
2. Twórz zasoby biznesowe i techniczne — Twórz zasoby biznesowe (dokumenty prawne i materiały marketingowe) oraz zasoby techniczne dla skojarzonego rozwiązania (obrazy kontenerów hostowane w Azure Container Registry.
3. Utwórz jednostkę SKU — Utwórz jednostki SKU skojarzone z ofertą. Dla każdego obrazu, który ma zostać opublikowany, wymagana jest unikatowa jednostka SKU.
4. Certyfikowanie i publikowanie oferty — po zakończeniu oferty i rozpoczęciu zasobów technicznych można przesłać ofertę. To przesyłanie rozpocznie proces publikowania. W trakcie tego procesu rozwiązanie jest testowane, weryfikowane, certyfikowane, a następnie "trafia na żywo" w portalu Azure Marketplace.

## <a name="next-steps"></a>Następne kroki

Przed zainstalowaniem tych kroków należy spełnić [wymagania techniczne i biznesowe](./cpp-prerequisites.md) dotyczące publikowania kontenera w Microsoft Azure Marketplace.
