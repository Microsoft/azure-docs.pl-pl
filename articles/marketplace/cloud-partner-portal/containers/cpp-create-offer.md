---
title: Tworzenie oferty kontenerów platformy Azure | Portal Azure Marketplace
description: Jak opublikować nową ofertę kontenera dla portalu Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: a7266d0f32a8ac18a4a76dee7eb3c39be253f7bd
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82148289"
---
# <a name="create-a-new-container-offer-with-the-cloud-partner-portal"></a>Utwórz nową ofertę kontenera z portal Cloud Partner

> [!IMPORTANT]
> Od 13 kwietnia 2020 rozpocznie się zarządzanie ofertami kontenerów platformy Azure w centrum partnerskim. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [Tworzenie oferty kontenera platformy Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer) , aby zarządzać migrowanymi ofertami.

W tym artykule opisano sposób tworzenia i publikowania wpisu oferty kontenera dla portalu Azure Marketplace. Każda oferta pojawia się jako jej własna jednostka w portalu Azure Marketplace i jest skojarzona z co najmniej jedną jednostką SKU.  Oferta kontenera składa się z następujących grup zasobów i usług pomocniczych:

|  **Grupa zasobów**   |  **Opis**  |
|  ---------------   |  ---------------  |
|    Jednostki SKU            |  Najmniejsza dostępna jednostka oferty. Pojedyncza oferta (Klasa produktu) może mieć wiele jednostek SKU skojarzonych z ofertą. Jednostek SKU można użyć do rozróżnienia między obsługiwanymi funkcjami i modelami rozliczeń. |
|  Portal Marketplace       | Zawiera zasoby i specyfikacje zarządzania marketingiem, prawną i liderem.  <ul><li> Zasoby marketingowe obejmują nazwę oferty, opis i logo</li> <li> Zasoby prawne obejmują zasady ochrony prywatności, warunki użytkowania i inne dokumenty prawne</li>  <li> Zasady zarządzania potencjalnymi klientami umożliwiają określenie sposobu obsługi potencjalnych klientów z poziomu portalu użytkowników końcowych platformy Azure Marketplace.</li> </ul> |
| Pomoc techniczna            | Zawiera informacje o kontakcie i zasadach |


## <a name="new-offer-form"></a>Formularz nowej oferty 

Zaloguj się do [Portal Cloud partner](https://cloudpartner.azure.com/), a następnie wybierz pozycję **+ Nowa oferta** na pasku menu po lewej stronie. W menu Nowa oferta wybierz pozycję **kontenery** , aby wyświetlić **nowy formularz oferty** i rozpocząć proces definiowania zasobów dla nowej oferty kontenera.

![Opcja wybierania kontenerów dla nowej oferty](./media/azure-container-offer.png)

## <a name="next-steps"></a>Następne kroki

**Nowa strona oferty** typu oferta kontenera zawiera zestaw kart i pól formularza, które będą używane do tworzenia nowej oferty. W każdym z poniższych artykułów wyjaśniono, jak za pomocą karty definiować grupy zasobów i usługi pomocnicze dla nowej oferty kontenera.

- [Karta ustawień oferty](./cpp-offer-settings-tab.md)
- [Karta jednostek SKU](./cpp-skus-tab.md)
- [Karta platformy handlowej](./cpp-marketplace-tab.md)
- [Karta pomocy technicznej](./cpp-support-tab.md)
