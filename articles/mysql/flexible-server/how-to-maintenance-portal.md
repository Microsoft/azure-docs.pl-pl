---
title: Azure Database for MySQL — elastyczny serwer (wersja zapoznawcza) — Planowana konserwacja — Azure Portal
description: Dowiedz się, jak skonfigurować ustawienia zaplanowanej konserwacji dla Azure Database for MySQL serwera z Azure Portal.
author: niklarin
ms.author: nlarin
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: c8251eb2a89a7481ebc981f2b89668c363651b39
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91315018"
---
# <a name="manage-scheduled-maintenance-settings-for-azure-database-for-mysql--flexible-server"></a>Zarządzanie ustawieniami zaplanowanej konserwacji dla Azure Database for MySQL — elastyczny serwer
 
Możesz określić opcje konserwacji dla każdego elastycznego serwera w ramach subskrypcji platformy Azure. Opcje obejmują harmonogram konserwacji i ustawienia powiadomień dla nadchodzących i zakończonych zdarzeń konserwacji.

> [!IMPORTANT]
> Azure Database for MySQL — serwer elastyczny jest w wersji zapoznawczej.
 
## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, musisz:
- [Serwer elastyczny Azure Database for MySQL](quickstart-create-server-portal.md)
 
## <a name="specify-maintenance-schedule-options"></a>Określ opcje harmonogramu konserwacji
 
1. Na stronie serwer MySQL w polu Nagłówek **ustawień** wybierz pozycję **konserwacja** , aby otworzyć Opcje zaplanowanej konserwacji.
2. Domyślny harmonogram (zarządzany przez system) to losowy dzień tygodnia, a 60-minutowa godzina rozpoczęcia konserwacji między 23:00 i 7am lokalnym serwerem. Jeśli chcesz dostosować ten harmonogram, wybierz pozycję **harmonogram niestandardowy**. Następnie można wybrać preferowany dzień tygodnia oraz okno 60-minutowy czasu rozpoczęcia konserwacji.
 
## <a name="notifications-about-scheduled-maintenance-events"></a>Powiadomienia o zdarzeniach zaplanowanej konserwacji
 
Za pomocą Azure Service Health można [wyświetlać powiadomienia](../../service-health/service-notifications.md) o nadchodzącej i zaplanowanej konserwacji na serwerze elastycznym. Możesz również [skonfigurować](../../service-health/resource-health-alert-monitor-guide.md) alerty w Azure Service Health, aby otrzymywać powiadomienia o zdarzeniach konserwacji.
 
## <a name="next-steps"></a>Następne kroki  
 
* Informacje o [zaplanowanej konserwacji w Azure Database for MySQL — elastyczny serwer](concepts-maintenance.md)
* Pochylenie o [Azure Service Health](../../service-health/overview.md)
