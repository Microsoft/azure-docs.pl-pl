---
author: v-amallick
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: v-amallick
ms.openlocfilehash: d20ed4d39921f8000f77f947c4372bd8b10ca642
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294147"
---
Możesz teraz używać [prywatnych punktów końcowych](../articles/private-link/private-endpoint-overview.md) do bezpiecznego wykonywania kopii zapasowych danych z serwerów w sieci wirtualnej do magazynu Recovery Services. Prywatny punkt końcowy używa adresu IP z przestrzeni adresowej sieci wirtualnej dla Twojego magazynu. Ruch sieciowy między zasobami w sieci wirtualnej a magazynem jest przesyłany przez sieć wirtualną i prywatny link w sieci szkieletowej firmy Microsoft. Eliminuje to narażenie z publicznego Internetu. Prywatne punkty końcowe mogą służyć do tworzenia kopii zapasowych i przywracania baz danych SQL i SAP HANA, które działają w ramach maszyn wirtualnych platformy Azure. Można go również użyć dla serwerów lokalnych przy użyciu agenta MARS.

Kopia zapasowa maszyny wirtualnej platformy Azure nie wymaga połączenia z Internetem i dlatego nie wymaga prywatnych punktów końcowych, aby umożliwić izolację sieci.

Przeczytaj więcej na temat prywatnych punktów końcowych Azure Backup [tym miejscu](../articles/backup/private-endpoints.md).