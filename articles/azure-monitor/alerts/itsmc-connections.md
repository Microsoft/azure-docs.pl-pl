---
title: łącznik zarządzania usługami IT w Azure Monitor
description: Ten artykuł zawiera informacje o sposobach łączenia narzędzia ITSM produktów/usług z usługą łącznik zarządzania usługami IT (ITSMC) w Azure Monitor, aby centralnie monitorować elementy robocze narzędzia ITSM i zarządzać nimi.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: 40e737a1ec5fb34cd22a08925143a100d36cdc6b
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103009321"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Łączenie produktów/usług ITSM za pomocą łącznika zarządzania usługami IT
Ten artykuł zawiera informacje dotyczące sposobu konfigurowania połączenia między produktem narzędzia ITSM/usługą a łącznik zarządzania usługami IT (ITSMC) w Log Analytics, aby centralnie zarządzać elementami roboczymi. Aby uzyskać więcej informacji na temat ITSMC, zobacz [Omówienie](./itsmc-overview.md).

Obsługiwane są następujące produkty/usługi narzędzia ITSM. Wybierz produkt, aby wyświetlić szczegółowe informacje na temat sposobu łączenia produktu z programem ITSMC.

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

> [!NOTE]
> Firma Microsoft proponuje naszym klientom Cherwell i Provance użycie [akcji elementu webhook](./action-groups.md#webhook) w Cherwell i Provance Endpoint jako innego rozwiązania do integracji.

## <a name="ip-ranges-for-itsm-partners-connections"></a>Zakresy adresów IP dla połączeń partnerów narzędzia ITSM
Aby wyświetlić listę adresów IP narzędzia ITSM, aby zezwolić na połączenia narzędzia ITSM z partnerów narzędzia ITSM Tools, zalecamy, aby wyświetlić cały publiczny zakres adresów IP w regionie świadczenia usługi Azure, w którym należy do obszaru roboczego LogAnalytics. [Szczegóły tutaj](https://www.microsoft.com/en-us/download/details.aspx?id=56519) W przypadku regionów EUS/UZE/EUS2/WUS2/Południowo-środkowe stany USA klient może wyświetlić listę tylko tag sieci z akcją.

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów w łączniku ITSM](./itsmc-resync-servicenow.md)
