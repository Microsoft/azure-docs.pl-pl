---
title: łącznik zarządzania usługami IT w Azure Monitor
description: Ten artykuł zawiera informacje o sposobach łączenia narzędzia ITSM produktów/usług z usługą łącznik zarządzania usługami IT (ITSMC) w Azure Monitor, aby centralnie monitorować elementy robocze narzędzia ITSM i zarządzać nimi.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: 7ac842df17e80b49990d89d1623330c4e31ab566
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100617933"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Łączenie produktów/usług ITSM za pomocą łącznika zarządzania usługami IT
Ten artykuł zawiera informacje dotyczące sposobu konfigurowania połączenia między produktem narzędzia ITSM/usługą a łącznik zarządzania usługami IT (ITSMC) w Log Analytics, aby centralnie zarządzać elementami roboczymi. Aby uzyskać więcej informacji na temat ITSMC, zobacz [Omówienie](../platform/itsmc-overview.md).

Obsługiwane są następujące produkty/usługi narzędzia ITSM. Wybierz produkt, aby wyświetlić szczegółowe informacje na temat sposobu łączenia produktu z programem ITSMC.

- [ServiceNow](../platform/itsmc-connections-servicenow.md)
- [System Center Service Manager](../platform/itsmc-connections-scsm.md)
- [Cherwell](../platform/itsmc-connections-cherwell.md)
- [Provance](../platform/itsmc-connections-provance.md)

> [!NOTE]
> Firma Microsoft proponuje naszym klientom Cherwell i Provance użycie [akcji elementu webhook](../platform/action-groups.md#webhook) w Cherwell i Provance Endpoint jako innego rozwiązania do integracji.

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów w łączniku ITSM](../platform/itsmc-resync-servicenow.md)