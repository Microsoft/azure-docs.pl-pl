---
title: Azure Database for PostgreSQL — elastyczny serwer (wersja zapoznawcza) — Planowana konserwacja — Azure Portal
description: Dowiedz się, jak skonfigurować ustawienia zaplanowanej konserwacji dla Azure Database for PostgreSQL serwera z Azure Portal.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: be6040b8b84a4b86746d62bd2f1c07f0ffea0a3b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91336296"
---
# <a name="manage-scheduled-maintenance-settings-for-azure-database-for-postgresql--flexible-server"></a>Zarządzanie ustawieniami zaplanowanej konserwacji dla Azure Database for PostgreSQL — elastyczny serwer
 
Możesz określić opcje konserwacji dla każdego elastycznego serwera w ramach subskrypcji platformy Azure. Opcje obejmują harmonogram konserwacji i ustawienia powiadomień dla nadchodzących i zakończonych zdarzeń konserwacji.

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, musisz:
- [Serwer elastyczny Azure Database for PostgreSQL](quickstart-create-server-portal.md)
 
## <a name="specify-maintenance-schedule-options"></a>Określ opcje harmonogramu konserwacji
 
1. Na stronie serwer PostgreSQL w polu Nagłówek **ustawień** wybierz pozycję **konserwacja** , aby otworzyć Opcje zaplanowanej konserwacji.
2. Domyślny harmonogram (zarządzany przez system) to losowy dzień tygodnia, a 60-minutowa godzina rozpoczęcia konserwacji między 23:00 i 7am lokalnym serwerem. Jeśli chcesz dostosować ten harmonogram, wybierz pozycję **harmonogram niestandardowy**. Następnie można wybrać preferowany dzień tygodnia oraz okno 60-minutowy czasu rozpoczęcia konserwacji.
 
## <a name="notifications-about-scheduled-maintenance-events"></a>Powiadomienia o zdarzeniach zaplanowanej konserwacji
 
Za pomocą Azure Service Health można [wyświetlać powiadomienia](../../service-health/service-notifications.md) o nadchodzącej i zaplanowanej konserwacji na serwerze elastycznym. Możesz również [skonfigurować](../../service-health/resource-health-alert-monitor-guide.md) alerty w Azure Service Health, aby otrzymywać powiadomienia o zdarzeniach konserwacji.
 
## <a name="next-steps"></a>Następne kroki  
 
* Informacje o [zaplanowanej konserwacji w Azure Database for PostgreSQL — elastyczny serwer](concepts-maintenance.md)
* Pochylenie o [Azure Service Health](../../service-health/overview.md)
