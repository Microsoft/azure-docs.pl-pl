---
title: Azure Database for PostgreSQL-Citus) — Planowana konserwacja — Azure Portal
description: Dowiedz się, jak skonfigurować ustawienia konserwacji zaplanowanej dla Azure Database for PostgreSQL ze skalowaniem (Citus) z Azure Portal.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 04dd37e3e9abbfbec7badb036802e645cc7732b0
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108519"
---
# <a name="manage-scheduled-maintenance-settings-for-azure-database-for-postgresql--hyperscale-citus"></a>Zarządzanie ustawieniami zaplanowanej konserwacji dla Azure Database for PostgreSQL — funkcja do skalowania (Citus)

W ramach subskrypcji platformy Azure można określić opcje konserwacji dla każdej grupy serwerów Citus. Opcje obejmują harmonogram konserwacji i ustawienia powiadomień dla nadchodzących i zakończonych zdarzeń konserwacji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, musisz:

- [Grupa serwerów Azure Database for PostgreSQL-ze skalą (Citus)](quickstart-create-hyperscale-portal.md)

## <a name="specify-maintenance-schedule-options"></a>Określ opcje harmonogramu konserwacji

1. Na stronie Grupa serwerów ze skalowaniem (Citus) w obszarze nagłówek **ustawień** wybierz pozycję **konserwacja** , aby otworzyć Opcje zaplanowanej konserwacji.
2. Domyślny harmonogram (zarządzany przez system) to losowy dzień tygodnia, a okno z 30-minutowym okresem konserwacji rozpoczyna się między 23:00 i 7amm [regionu platformy Azure](https://go.microsoft.com/fwlink/?linkid=2143646). Jeśli chcesz dostosować ten harmonogram, wybierz pozycję **harmonogram niestandardowy**. Następnie można wybrać preferowany dzień tygodnia i 30-minutowy okres czasu rozpoczęcia konserwacji.

## <a name="notifications-about-scheduled-maintenance-events"></a>Powiadomienia o zdarzeniach zaplanowanej konserwacji

Za pomocą Azure Service Health można [wyświetlać powiadomienia](../service-health/service-notifications.md) dotyczące nadchodzącej i wcześniejszej zaplanowanej konserwacji w grupie serwerów Citus. Możesz również [skonfigurować](../service-health/resource-health-alert-monitor-guide.md) alerty w Azure Service Health, aby otrzymywać powiadomienia o zdarzeniach konserwacji.

## <a name="next-steps"></a>Następne kroki

* Informacje o [zaplanowanej konserwacji w Azure Database for PostgreSQL — Citus](concepts-hyperscale-maintenance.md)
* Pochylenie o [Azure Service Health](../service-health/overview.md)
