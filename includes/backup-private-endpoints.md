---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: 024f70a21b36bffa662ba6f568adc8efbd0a909d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81539596"
---
Teraz można użyć [prywatnych punktów końcowych](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) do bezpiecznego tworzenia kopii zapasowych danych z serwerów w sieci wirtualnej do magazynu usług odzyskiwania. Prywatny punkt końcowy używa adresu IP z przestrzeni adresowej sieci wirtualnej dla magazynu. Ruch sieciowy między zasobami wewnątrz sieci wirtualnej a przechowalnią jest przesyłany przez sieć wirtualną i prywatne łącze w sieci szkieletowej firmy Microsoft. Eliminuje to narażenie z publicznego internetu. Prywatne punkty końcowe mogą służyć do tworzenia kopii zapasowych i przywracania baz danych SQL i SAP HANA, które są uruchamiane wewnątrz maszyn wirtualnych platformy Azure. Może być również używany dla serwerów lokalnych przy użyciu agenta MARS.

Tworzenie kopii zapasowej maszyny Wirtualnej platformy Azure nie wymaga łączności z Internetem, a więc nie wymaga prywatnych punktów końcowych, aby umożliwić izolację sieci.

>[!NOTE]
> Ta funkcja jest obecnie w ograniczonej dostępności i jest dostępna w west central US, South Central US, West US 2 i East US (dostępność w innych regionach platformy Azure do naśladowania). Wypełnij [tę ankietę](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) i azbackupnetsec@microsoft.com wyślij do nas wiadomość e-mail, jeśli jesteś zainteresowany korzystaniem z prywatnych punktów końcowych dla usługi Azure Backup. Możliwość korzystania z tej funkcji podlega zatwierdzeniu przez usługę Azure Backup.
