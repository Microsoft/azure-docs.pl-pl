---
title: Tworzenie oferty maszyny wirtualnej w portalu Azure Marketplace
description: Zawiera listę kroków wymaganych do utworzenia nowej oferty maszyny wirtualnej w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: a25f6877f1fb4940fb1de127b81d83975c8e835c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82142706"
---
# <a name="create-virtual-machine-offer"></a>Utwórz ofertę maszyny wirtualnej

> [!IMPORTANT]
> Od 13 kwietnia 2020 rozpocznie się zarządzanie ofertami usługi Azure Virtual Machine w centrum partnerskim. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [Tworzenie oferty maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) w celu zarządzania zmigrowanymi ofertami.

W tej sekcji przedstawiono kroki wymagane do utworzenia nowego żądania oferty maszyny wirtualnej w witrynie Azure Marketplace.  Każda oferta pojawia się jako jej własna jednostka w portalu Azure Marketplace i jest skojarzona z co najmniej jedną jednostką SKU.  Oferta maszyny wirtualnej składa się z następujących grup zasobów i usług pomocniczych: 

![Zasoby dla oferty maszyny wirtualnej](./media/publishvm_002.png)

gdzie:

|  **Grupa zasobów**   |  **Opis**  |
|  ---------------   |  ---------------  |
|    Jednostki SKU            |  Najmniejsza jednostka jednostek oferty. Z jedną ofertą (Klasa produktu) może być skojarzonych wiele jednostek SKU, aby można było rozróżnić obsługiwane funkcje, typy obrazów maszyn wirtualnych i modele rozliczeń. |
|  Portal Marketplace       | Zawiera zasoby i specyfikacje zarządzania marketingiem, prawną i liderem.  <ul><li> Zasoby marketingowe obejmują nazwę oferty, opis i logo</li> <li> Zasoby prawne obejmują zasady ochrony prywatności, warunki użytkowania i inne dokumenty prawne</li>  <li> Zasady zarządzania potencjalnymi klientami umożliwiają określenie sposobu obsługi potencjalnych klientów z poziomu portalu użytkowników końcowych platformy Azure Marketplace.</li> </ul> |
| Pomoc techniczna            | Zawiera informacje o kontakcie i zasadach |
| Wersja testowa         | Definiuje zasoby, które umożliwiają użytkownikom końcowym testowanie oferty przed ich zakupieniem |
|  |  |


## <a name="new-offer-form"></a>Formularz nowej oferty

Po zalogowaniu się do [Portal Cloud partner](https://cloudpartner.azure.com/)kliknij pozycję **+ nowy element oferty** na lewym pasku menu. W menu wyniki kliknij pozycję **Virtual Machines** , aby wyświetlić nowy formularz **oferty** i rozpocząć proces definiowania zasobów dla nowej oferty maszyny wirtualnej. 
<!-- not all publishers see corevm or azure apps test, you need to be whitelisted to see them. we should hide those in these images. -->

![Wybór interfejsu użytkownika dla nowej maszyny wirtualnej](./media/publishvm_003.png)

> [!WARNING]
> Jeśli opcja **Virtual Machines** nie jest wyświetlana lub nie jest włączona, Twoje konto nie ma uprawnień do tworzenia tego typu oferty.  Upewnij się, że spełniono wszystkie [wymagania wstępne](./cpp-prerequisites.md) dla tego typu oferty, w tym rejestrowanie dla konta dewelopera.


## <a name="next-steps"></a>Następne kroki

W kolejnych tematach w tej sekcji są dublowane karty na **nowej stronie oferty** (dla typu oferty maszyny wirtualnej).  W każdym artykule wyjaśniono, jak za pomocą skojarzonej karty definiować grupy zasobów i usługi pomocnicze dla nowej oferty maszyn wirtualnych.

- [Karta ustawień oferty](./cpp-offer-settings-tab.md)
- [Karta jednostek SKU](./cpp-skus-tab.md)
- [Karta Wersja testowa](./cpp-test-drive-tab.md)
- [Karta platformy handlowej](./cpp-marketplace-tab.md)
- [Karta pomocy technicznej](./cpp-support-tab.md)
