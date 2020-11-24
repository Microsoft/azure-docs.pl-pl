---
title: Obsługa przenoszenia zasobów usługi Azure SQL między regionami przy użyciu funkcji przenoszenia zasobów platformy Azure.
description: Zapoznaj się z obsługą przenoszenia zasobów usługi Azure SQL między regionami przy użyciu usługi Azure Resource przenoszącej.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: raynew
ms.openlocfilehash: 573d52b836aef36063dd288bf5a5016b98d220ef
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95524134"
---
# <a name="support-for-moving-azure-sql-resources-between-azure-regions"></a>Obsługa przeniesienia zasobów usługi Azure SQL między regionami platformy Azure

W tym artykule podsumowano obsługę i wymagania wstępne dotyczące przenoszenia zasobów usługi Azure SQL między regionami platformy Azure za pomocą usługi Azure Resource przenoszącej.

## <a name="requirements"></a>Wymagania

Wymagania zostały podsumowane w poniższej tabeli.

**Funkcja** | **Obsługiwane/nieobsługiwane** | **Szczegóły**
--- | --- | ---
**Skalowanie Azure SQL Database** | Nieobsługiwane | Nie można przenieść baz danych w warstwie usługi Azure SQL — skalowanie przy użyciu funkcji przenoszenia zasobów.
**Nadmiarowość stref** | Obsługiwane |  Obsługiwane opcje przenoszenia:<br/><br/> -Między regionami, które obsługują nadmiarowość stref.<br/><br/> -Między regionami, które nie obsługują nadmiarowości stref.<br/><br/> — Między regionem, który obsługuje nadmiarowość strefy, do regionu, który nie obsługuje nadmiarowości strefy.<br/><br/> — Między regionem, który nie obsługuje nadmiarowości strefy w regionie, który obsługuje nadmiarowość strefy. 
**Synchronizacja danych** | Baza danych centrum/Synchronizacja: nieobsługiwane<br/><br/> Element członkowski synchronizacji: obsługiwane. | Jeśli element członkowski synchronizacji jest przenoszony, należy skonfigurować synchronizację danych z nową docelową bazą danych.
**Istniejąca replikacja geograficzna** | Obsługiwane | Istniejące repliki geograficzne są ponownie mapowane na nowe podstawowe w regionie docelowym.<br/><br/> Po przeniesieniu należy zainicjować rozmieszczanie. [Dowiedz się więcej](../azure-sql/database/active-geo-replication-configure-portal.md)
**Transparent Data Encryption (TDE) z Bring Your Own Key (BYOK)** | Obsługiwane | [Dowiedz się więcej](../key-vault/general/move-region.md) o przenoszeniu magazynów kluczy między regionami.
**TDE z kluczem zarządzanym przez usługę** | Obsługiwane. |  [Dowiedz się więcej](../key-vault/general/move-region.md) o przenoszeniu magazynów kluczy między regionami.
**Dynamiczne reguły maskowania danych** | Obsługiwane. | Reguły są automatycznie kopiowane do regionu docelowego w ramach przenoszenia. [Dowiedz się więcej](../azure-sql/database/dynamic-data-masking-configure-portal.md).
**Zaawansowane zabezpieczenia danych** | Nieobsługiwane. | Obejście: Skonfiguruj na poziomie SQL Server w regionie docelowym. [Dowiedz się więcej](../azure-sql/database/azure-defender-for-sql.md).
**Reguły zapory** | Nieobsługiwane. | Obejście: Skonfiguruj reguły zapory dla SQL Server w regionie docelowym. Reguły zapory na poziomie bazy danych są kopiowane z serwera źródłowego na serwer docelowy. [Dowiedz się więcej](../azure-sql/database/firewall-create-server-level-portal-quickstart.md).
**Zasady inspekcji** | Nieobsługiwane. | Zasady zostaną zresetowane do domyślnych po przeniesieniu. [Dowiedz się](../azure-sql/database/auditing-overview.md) , jak zresetować.
**Przechowywanie kopii zapasowej** | Obsługiwane. | Zasady przechowywania kopii zapasowej dla źródłowej bazy danych są przenoszone do docelowej bazy danych. [Dowiedz się](../azure-sql/database/long-term-backup-retention-configure.md) , jak modyfikować ustawienia po przeniesieniu.
**Dostrajanie automatycznego** | Nieobsługiwane. | Obejście: Ustaw ustawienia automatycznego dostrajania po przeniesieniu. [Dowiedz się więcej](../azure-sql/database/automatic-tuning-enable.md).
**Alerty bazy danych** | Nieobsługiwane. | Obejście: Ustaw alerty po przeniesieniu. [Dowiedz się więcej](../azure-sql/database/alerts-insights-configure-portal.md).
**Usługa Azure SQL Server rozciągana baza danych** | Nieobsługiwane | Nie można przenieść rozproszonych baz danych programu SQL Server za pomocą przenoszenia zasobów.
**Azure Synapse Analytics** | Nieobsługiwane | Nie można przenieść Synapse Analytics (dawniej SQL Data Warehouse) za pomocą przenoszenia zasobów.
## <a name="next-steps"></a>Następne kroki

Wypróbuj [zasoby SQL platformy Azure](tutorial-move-region-sql.md) w innym regionie przy użyciu obszaru przenoszenia zasobów.