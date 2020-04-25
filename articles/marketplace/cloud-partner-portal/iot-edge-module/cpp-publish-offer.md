---
title: Oferta publikowania Azure IoT Edge modułu | Portal Azure Marketplace
description: Jak opublikować ofertę modułu IoT Edge.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: ef73956b1b0d3e7bed6e91cde0b92bcc3e432795
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82141840"
---
# <a name="publish-iot-edge-module-offer"></a>Publikowanie oferty modułu usługi IoT Edge

>[!Important]
>Od 13 kwietnia 2020 rozpocznie się Zarządzanie ofertą modułu IoT Edge w centrum partnerskim. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [Create an IoT Edge module](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation) , aby zarządzać migrowanymi ofertami.

 Po utworzeniu nowej oferty, podając informacje na **nowej stronie oferty** , można opublikować ofertę. Wybierz pozycję **Publikuj** , aby rozpocząć proces publikowania.

Na poniższym diagramie przedstawiono główne kroki w procesie publikowania dla oferty "go Live".

![Kroki publikowania dla oferty modułu IoT Edge](./media/iot-edge-module-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Szczegółowy opis kroków publikowania

W poniższej tabeli opisano każdy krok publikowania, z oszacowaniem czasu (maksimum) w celu ukończenia każdego kroku.
<!-- P2: we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Etap publikowania**           | **Pierwszym**    | **Opis**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Sprawdzanie wymagań wstępnych         | 15 minut   | Informacje o ofercie i ustawienia oferty są weryfikowane.                        |
| Certyfikacja                  | 2 tygodnie | Oferta jest analizowana przez zespół certyfikacji platformy Azure. Ten krok spowoduje wykonanie skanowania w poszukiwaniu wirusów, złośliwego oprogramowania, zgodności z bezpieczeństwem i problemów z zabezpieczeniami. Zostanie również zweryfikowana, że ta oferta modułu IoT Edge spełnia wszystkie kryteria kwalifikujące (zobacz [wymagania wstępne](./cpp-prerequisites.md) i [przygotowania zasobów technicznych](./cpp-create-technical-assets.md)). Opinie są podawane w przypadku znalezienia problemu. |
| Tworzenie pakietów | 1 godzina  | Zasoby techniczne oferty są pakowane do użytku przez klienta, a systemy liderów są skonfigurowane i konfigurowane. |
|  Wylogowanie wydawcy             |  -        | Końcowe przeglądy i potwierdzenie wydawcy przed rozpoczęciem oferty. Ofertę można wdrożyć w wybranych subskrypcjach (w krokach dotyczących informacji o ofercie), aby sprawdzić, czy spełnia ona wszystkie wymagania.  Wybierz pozycję **Przejdź na żywo** , aby Twoja oferta mogła przejść do następnego kroku. |
| Tworzenie pakietów                 | 1 godzina | Końcowa oferta jest replikowana w systemach i regionach produkcyjnych w portalu Marketplace. | 
| Na żywo                           | 4 dni |Oferta jest wydana, replikowana do wymaganych regionów i udostępniona publicznie. |

Zezwalaj na zakończenie procesu publikowania przez maksymalnie 10 dni roboczych i oferowana jest oferta. Po zakończeniu procesu publikowania w [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)zostanie wyświetlona oferta modułu IoT Edge.

## <a name="next-steps"></a>Następne kroki

- [Aktualizowanie istniejącej oferty modułu IoT Edge w portalu Azure Marketplace](./cpp-update-existing-offer.md)
