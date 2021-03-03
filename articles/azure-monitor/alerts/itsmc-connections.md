---
title: łącznik zarządzania usługami IT w Azure Monitor
description: Ten artykuł zawiera informacje o sposobach łączenia narzędzia ITSM produktów/usług z usługą łącznik zarządzania usługami IT (ITSMC) w Azure Monitor, aby centralnie monitorować elementy robocze narzędzia ITSM i zarządzać nimi.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: a372cdcd05267f3bdb093f676948a79c473ad955
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734778"
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

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów w łączniku ITSM](./itsmc-resync-servicenow.md)