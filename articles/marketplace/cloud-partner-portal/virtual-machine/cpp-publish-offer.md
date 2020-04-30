---
title: Publikowanie oferty maszyny wirtualnej w portalu Azure Marketplace
description: Zawiera listę kroków wymaganych do opublikowania istniejącej oferty maszyny wirtualnej w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: dsindona
ms.openlocfilehash: f66ce0c15e976898d5022bf5705a82fe0969ec07
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82147654"
---
# <a name="publish-a-virtual-machine-offer"></a>Publikowanie oferty maszyny wirtualnej

> [!IMPORTANT]
> Od 13 kwietnia 2020 rozpocznie się zarządzanie ofertami usługi Azure Virtual Machine w centrum partnerskim. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [Tworzenie oferty maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) w celu zarządzania zmigrowanymi ofertami.

 Ostatnim krokiem po zdefiniowaniu oferty w portalu i utworzeniu skojarzonych zasobów technicznych jest przesłanie oferty do opublikowania. Na poniższym diagramie przedstawiono główne kroki w procesie publikowania do "go Live":

![Kroki publikowania dla oferty maszyny wirtualnej](./media/publishvm_013.png)

W poniższej tabeli opisano te kroki i zawarto maksymalne oszacowanie czasu dla ich ukończenia:
<!-- we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Etap publikowania**           | **Pierwszym**    | **Opis**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Sprawdzanie wymagań wstępnych         | 15 minut   | Informacje o ofercie i ustawienia oferty są weryfikowane.                        |
| Sprawdzanie poprawności dysku testowego (opcjonalnie) | 2 godziny | Jeśli wybrano opcję włączenia dysku testowego, firma Microsoft zweryfikuje konfigurację dysku testowego, jego wdrożenie i replikację za pomocą wybranych regionów. |
| Certyfikacja                  | 3 dni | Oferta jest analizowana przez zespół certyfikacji platformy Azure. Ten krok spowoduje wykonanie skanowania w poszukiwaniu wirusów, złośliwego oprogramowania, zgodności z bezpieczeństwem i problemów z zabezpieczeniami. Opinie są podawane w przypadku znalezienia problemu. |
| Inicjowanie obsługi                   | 4 dni   | Oferta maszyny wirtualnej jest replikowana w systemach produkcyjnych w portalu Marketplace.               |
| Pakowanie i rejestracja generacji potencjalnego klienta | \<1 godzina  | Zasoby techniczne oferty są pakowane do użytku przez klienta, a systemy liderów są skonfigurowane i konfigurowane. |
|  Wydawca przygotowania             |  -        | Końcowe przeglądy i potwierdzenie wydawcy przed rozpoczęciem oferty. Ofertę można wdrożyć w wybranych subskrypcjach (w krokach dotyczących informacji o ofercie), aby sprawdzić, czy spełnia ona wszystkie wymagania.  |
| Inicjowanie obsługi                   | 4 dni | Zagotowana oferta maszyny wirtualnej jest replikowana w systemach i regionach produkcyjnych w portalu Marketplace. | 
| Na żywo                           | 4 dni | Oferta maszyny wirtualnej jest wydawana, replikowana do wymaganych regionów i udostępniona publicznie. |
|  |  |

Poczekaj maksymalnie 16 dni na ukończenie tego procesu.  Po wykonaniu tych kroków publikacji w [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/)zostanie wyświetlona oferta maszyny wirtualnej. 

