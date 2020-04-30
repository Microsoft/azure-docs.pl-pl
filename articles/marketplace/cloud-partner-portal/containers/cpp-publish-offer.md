---
title: Oferta publikowania obrazu kontenerów platformy Azure | Portal Azure Marketplace
description: Jak opublikować ofertę kontenera platformy Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: 58e096a3b25b16e54cf2f18935dcf4a2d44cd767
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82146211"
---
# <a name="publish-container-offer"></a>Publikowanie oferty kontenera

> [!IMPORTANT]
> Od 13 kwietnia 2020 rozpocznie się zarządzanie ofertami kontenerów platformy Azure w centrum partnerskim. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [Tworzenie oferty kontenera platformy Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer) , aby zarządzać migrowanymi ofertami.

 Po utworzeniu nowej oferty przy użyciu nowej strony **oferty** można opublikować tę ofertę. Wybierz pozycję **Publikuj** , aby rozpocząć proces publikowania.

Na poniższym diagramie przedstawiono główne kroki w procesie publikowania dla oferty "go Live".

![Kroki publikowania dla oferty kontenera](./media/offer-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Szczegółowy opis kroków publikowania

W poniższej tabeli opisano każdy krok publikacji. Podano również szacowany czas na zakończenie każdego kroku.


|  **Etap publikowania**           | **Pierwszym**    | **Opis**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Sprawdzanie wymagań wstępnych         | 15 minut   | Informacje o ofercie i ustawienia oferty są weryfikowane.                        |
| Certyfikacja                  | 1 tydzień | Oferta jest analizowana przez zespół certyfikacji platformy Azure. Oferta jest skanowana pod kątem wirusów, złośliwego oprogramowania, zgodności z zasadami bezpieczeństwa i problemów z zabezpieczeniami. Oferta jest sprawdzana, czy spełnia wszystkie kryteria kwalifikacyjne. Aby uzyskać więcej informacji, zobacz [wymagania wstępne](./cpp-prerequisites.md) i [Przygotowywanie zasobów technicznych](./cpp-create-technical-assets.md). Opinie podane w przypadku znalezienia problemu. |
| Tworzenie pakietów | 1 godzina  | Zasoby techniczne oferty są pakowane do użytku przez klienta, a systemy liderów są skonfigurowane i konfigurowane. |
|  Wylogowanie wydawcy             |  -        | Końcowe przeglądy i potwierdzenie wydawcy przed rozpoczęciem oferty. Ofertę można wdrożyć w wybranych subskrypcjach (w krokach dotyczących informacji o ofercie), aby sprawdzić, czy spełnia ona wszystkie wymagania.  Wybierz pozycję **Przejdź na żywo** , aby Twoja oferta mogła przejść do następnego kroku. |
| Tworzenie pakietów                 | 1 godzina | Zakończona oferta jest replikowana w systemach i regionach produkcyjnych w portalu Marketplace. | 
| Na żywo                           | 4 dni |Oferta jest wydana, replikowana do wymaganych regionów i udostępniona publicznie. |

Zezwalaj na zakończenie procesu publikowania przez maksymalnie 10 dni roboczych i oferowana jest oferta. Po zakończeniu procesu publikowania w [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)zostanie wyświetlona oferta kontenera.

## <a name="next-steps"></a>Następne kroki

[Aktualizowanie istniejącej oferty kontenera w witrynie Azure Marketplace](./cpp-update-existing-offer.md)
