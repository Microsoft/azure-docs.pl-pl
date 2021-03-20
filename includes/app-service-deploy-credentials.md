---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 04/20/2020
ms.author: cephalin
ms.openlocfilehash: c3fa57dd162fbbfbf0d46f73bffc78f279ef2968
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "83649132"
---
* **Poświadczenia na poziomie użytkownika**: jeden zestaw poświadczeń dla całego konta platformy Azure. Można go użyć do wdrożenia programu w celu App Service dla każdej aplikacji w dowolnej subskrypcji, do której konto platformy Azure ma uprawnienia dostępu. Jest to domyślny zestaw, który znajduje się w graficznym interfejsie użytkownika portalu (na przykład **Omówienie** i **Właściwości** [strony zasobu](../articles/azure-resource-manager/management/manage-resources-portal.md#manage-resources)aplikacji). Gdy użytkownik uzyskuje dostęp do aplikacji za pośrednictwem Role-Based Access Control (RBAC) lub współadministratora, może użyć własnych poświadczeń na poziomie użytkownika do momentu odwołania dostępu. Nie udostępniaj tych poświadczeń innym użytkownikom platformy Azure.

* **Poświadczenia na poziomie aplikacji**: jeden zestaw poświadczeń dla każdej aplikacji. Można go użyć do wdrożenia tylko w tej aplikacji. Poświadczenia dla każdej aplikacji są generowane automatycznie podczas tworzenia aplikacji. Nie można ich skonfigurować ręcznie, ale można je zresetować w dowolnym momencie. Aby użytkownik mógł uzyskać dostęp do poświadczeń na poziomie aplikacji za pośrednictwem (RBAC), użytkownik musi mieć uprawnienia współautora lub wyższą w aplikacji (w tym wbudowana rola współautor witryny sieci Web). Czytelnicy nie mogą publikować i nie mogą uzyskać dostępu do tych poświadczeń.