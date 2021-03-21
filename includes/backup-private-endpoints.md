---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: fd7124ad83a446d7dde39a836c337a97b12a0a0a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95561974"
---
Możesz teraz używać [prywatnych punktów końcowych](../articles/private-link/private-endpoint-overview.md) do bezpiecznego wykonywania kopii zapasowych danych z serwerów w sieci wirtualnej do magazynu Recovery Services. Prywatny punkt końcowy używa adresu IP z przestrzeni adresowej sieci wirtualnej dla Twojego magazynu. Ruch sieciowy między zasobami w sieci wirtualnej a magazynem jest przesyłany przez sieć wirtualną i prywatny link w sieci szkieletowej firmy Microsoft. Eliminuje to narażenie z publicznego Internetu. Prywatne punkty końcowe mogą służyć do tworzenia kopii zapasowych i przywracania baz danych SQL i SAP HANA, które działają w ramach maszyn wirtualnych platformy Azure. Można go również użyć dla serwerów lokalnych przy użyciu agenta MARS.

Kopia zapasowa maszyny wirtualnej platformy Azure nie wymaga połączenia z Internetem i dlatego nie wymaga prywatnych punktów końcowych, aby umożliwić izolację sieci.

Przeczytaj więcej na temat prywatnych punktów końcowych Azure Backup [tym miejscu](../articles/backup/private-endpoints.md).