---
title: Oferta maszyny wirtualnej w portalu Azure Marketplace
description: Przegląd procesu publikowania oferty maszyny wirtualnej w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: dsindona
ms.openlocfilehash: b857cf8b7485beb884adf77d99f82d965f55a0ad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82142797"
---
# <a name="virtual-machine-offer"></a>Oferta maszyny wirtualnej

> [!IMPORTANT]
> Od 13 kwietnia 2020 rozpocznie się zarządzanie ofertami usługi Azure Virtual Machine w centrum partnerskim. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [Tworzenie oferty maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) w celu zarządzania zmigrowanymi ofertami.

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| W tej sekcji opisano sposób publikowania nowej oferty maszyny wirtualnej w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com). Pomoc techniczna jest świadczona zarówno dla maszyn wirtualnych opartych na systemie Windows, jak i Linux, zawierających wirtualny dysk twardy (VHD) systemu operacyjnego i zero lub więcej dysków VHD z danymi. | ![ikona maszyny wirtualnej](./media/virtual-machine-icon.png)  |


## <a name="publishing-overview"></a>Przegląd publikowania

Poniższy film wideo [optymalizuje ofertę portalu Azure Marketplace](https://channel9.msdn.com/Events/Build/2017/P4026?ocid=player), przedstawia szerokie Omówienie portalu Azure Marketplace, w tym informacje dotyczące publikowania na tym rynku (przy użyciu rozwiązania maszyny wirtualnej), sposobu optymalizowania środowiska użytkownika przy użyciu strony produktu oraz opcjonalnego środowiska testowego testowania, sposobu generowania potencjalnych klientów i ich używania oraz optymalizowania zaangażowania klientów.

> [!VIDEO https://channel9.msdn.com/Events/Build/2017/P4026/player]


## <a name="vm-publishing-process-flow"></a>Przepływ procesu publikowania maszyny wirtualnej

Na poniższym diagramie przedstawiono ogólne kroki publikowania oferty maszyn wirtualnych. 

![Proces publikowania maszyn wirtualnych](./media/publishvm_001.png)

1. Utwórz ofertę — wszystkie szczegóły i informacje o ofercie zostały skonfigurowane, w tym opis oferty, materiały marketingowe, informacje o pomocy technicznej i specyfikacje zasobów.

2. Twórz zasoby biznesowe i techniczne — Twórz zasoby biznesowe (dokumenty prawne i materiały marketingowe) oraz zasoby techniczne dla skojarzonego rozwiązania (tutaj: maszyny wirtualne i dołączone dyski). 

3. Utwórz jednostkę SKU — Utwórz skojarzone jednostki SKU skojarzone z ofertą i prześlij je.  Dla każdego obrazu, który ma zostać opublikowany, wymagana jest unikatowa jednostka SKU. 
 
4. Certyfikowanie i publikowanie oferty — po zakończeniu oferty i zasobów technicznych można przesłać ofertę. To zgłoszenie rozpocznie proces publikowania, w którym rozwiązanie zostanie przetestowane, zweryfikowane, certyfikowane, a następnie "trafia na żywo" w portalu Marketplace.  

## <a name="next-steps"></a>Następne kroki

Przed zainstalowaniem tych kroków należy spełnić [wymagania techniczne i biznesowe](./cpp-prerequisites.md) dotyczące publikowania maszyny wirtualnej w Microsoft Azure Marketplace. 
