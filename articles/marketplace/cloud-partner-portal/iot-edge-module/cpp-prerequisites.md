---
title: Wymagania wstępne dotyczące Azure IoT Edge modułu | Portal Azure Marketplace
description: Wymagania wstępne dotyczące publikowania modułu IoT Edge.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: d1bfc29a0cb031421b4017454bc8a2cdd8be09f9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82142366"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>Wymagania wstępne dotyczące publikowania modułu IoT Edge

>[!Important]
>Od 13 kwietnia 2020 rozpocznie się Zarządzanie ofertą modułu IoT Edge w centrum partnerskim. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [Create an IoT Edge module](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation) , aby zarządzać migrowanymi ofertami.

W tym artykule opisano wymagania wstępne dotyczące publikowania oferty modułu IoT Edge.  Jeśli jeszcze tego nie zrobiono, zapoznaj się z [przewodnikiem publikowania modułów IoT Edge](../..//iot-edge-module.md).


## <a name="publishing-prerequisites"></a>Publikowanie wymagań wstępnych

Aby opublikować moduł IoT Edge w portalu Azure Marketplace, należy spełnić następujące wymagania wstępne:

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- Dostęp do [Portal Cloud partner](https://cloudpartner.azure.com/). Aby uzyskać więcej informacji, zobacz artykuł [Azure Marketplace i Podręcznik publikowania AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Umowa dotycząca [warunków korzystania z witryny Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace-terms/)
- Hostowanie zasobów technicznych modułu IoT Edge w Azure Container Registry.  Aby uzyskać więcej informacji, zobacz [jak przygotować zasób techniczny modułu IoT Edge](./cpp-create-technical-assets.md)
- Przygotuj metadane modułu IoT Edge do użycia. Na przykład Przygotuj następujące zasoby:
    - Tytuł
    - Opis (w formacie HTML)
    - Obraz logo (format PNG i stałe rozmiary obrazu, w tym 40x40px, 90x90px, 115x115px, 255x115px)
    - Warunki użytkowania i zasady zachowania poufności informacji
    - Domyślna konfiguracja modułu obejmująca: trasy, odpowiednie właściwości, opcje i zmienne środowiskowe.
    - Dokumentacja modułu
    - Kontakt z pomocą techniczną


## <a name="next-steps"></a>Następne kroki

Po przywróceniu [zasobów technicznych modułu IoT Edge](./cpp-create-technical-assets.md)będziesz mieć możliwość [utworzenia oferty modułu IoT Edge](./cpp-create-offer.md). 
